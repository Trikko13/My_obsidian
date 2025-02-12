https://platform.stratascratch.com/coding/10354-most-profitable-companies?code_type=1
Find the 3 most profitable companies in the world. Sort the result based on profits in descending order. If multiple companies have the same profit, assign them the same rank and include all tied companies in the top results. Output the result along with the corresponding company name.

WITH performance_profit AS (
    SELECT 
        company, 
        profits, 
        DENSE_RANK() OVER (ORDER BY profits DESC) AS ranking
    FROM 
        forbes_global_2010_2014
    GROUP BY 
        company, profits
)
SELECT 
    company, 
    profits
FROM 
    performance_profit
WHERE 
    ranking BETWEEN 1 AND 3


https://platform.stratascratch.com/coding/10352-users-by-avg-session-time/solutions?code_type=1


Calculate each user's average session time, where a session is defined as the time difference between a page_load and a page_exit. Assume each user has only one session per day. If there are multiple page_load or page_exit events on the same day, use only the latest page_load and the earliest page_exit, ensuring the page_load occurs before the page_exit. Output the user_id and their average session time.

WITH **page_load_actions** AS (
    SELECT
        user_id,
        timestamp AS page_load_timestamp
    FROM facebook_web_log
    WHERE action = 'page_load'
    ORDER BY user_id,timestamp
     ),
     **page_exit_actions** AS (
    SELECT
        user_id,
        timestamp AS page_exit_timestamp
    FROM facebook_web_log
    WHERE action = 'page_exit'
    ORDER BY user_id,timestamp
     ),
     **user_page_session_times** AS (
    SELECT
        l.user_id,
        CAST(page_load_timestamp AS DATE) AS page_load_date,
        CAST(page_exit_timestamp AS DATE) AS page_exit_date,
        page_exit_timestamp - page_load_timestamp AS session_time,
        RANK() OVER w AS relevant_session_rank
    FROM page_load_actions AS l
    LEFT JOIN page_exit_actions AS e
        USING (user_id)
    WHERE page_load_timestamp <= page_exit_timestamp -- conditions as per question
      AND CAST(page_load_timestamp AS DATE) = CAST(page_exit_timestamp AS DATE)
    WINDOW w AS (PARTITION BY user_id,CAST(page_load_timestamp AS DATE) ORDER BY page_load_timestamp DESC,page_exit_timestamp ASC) -- finding latest session load time
         )
SELECT
    user_id,
    AVG(session_time) AS avg_session_time
FROM user_page_session_times
WHERE relevant_session_rank = 1
GROUP BY user_id
;

Давай разберём это **ещё глубже**, разложим каждый шаг по мельчайшим деталям, чтобы всё стало понятно. Постараюсь объяснить максимально доступно.

---

### **1. Что делает задача?**

Мы хотим найти **среднее время сессии** для каждого пользователя, где:

- Сессия определяется как время между последним `page_load` и первым `page_exit` в рамках одного дня.
- Если на один день приходится несколько сессий, берём только **последнюю сессию**.

---

### **2. Пошаговый разбор**

#### **Шаг 1: Разделяем данные**

Сначала мы разбиваем данные на две группы:

- **`page_load_actions`:** Содержит только события `page_load`.
- **`page_exit_actions`:** Содержит только события `page_exit`.

**Почему это важно?**

- Действия (`page_load` и `page_exit`) находятся в одной колонке `action`.
- Нам нужно сначала их разделить, чтобы:
    - Найти последнее `page_load` для пользователя и дня.
    - Найти первое `page_exit` для пользователя и дня.

---

#### **Шаг 2: Создаём подзапросы**

Теперь мы работаем с двумя временными таблицами:

1. **`page_load_actions`:**
    
    - Для каждого пользователя и дня находим самое позднее событие `page_load`.
    
    ```sql
    WITH page_load_actions AS (
        SELECT
            user_id,
            timestamp AS page_load_timestamp
        FROM facebook_web_log
        WHERE action = 'page_load'
        ORDER BY user_id, timestamp
    )
    ```
    
    - `MAX(timestamp)` здесь не нужен сразу, потому что ранжирование будет позже.
2. **`page_exit_actions`:**
    
    - Для каждого пользователя и дня находим самое раннее событие `page_exit`.
    
    ```sql
    WITH page_exit_actions AS (
        SELECT
            user_id,
            timestamp AS page_exit_timestamp
        FROM facebook_web_log
        WHERE action = 'page_exit'
        ORDER BY user_id, timestamp
    )
    ```
    

---

#### **Шаг 3: Соединяем `page_load_actions` и `page_exit_actions`**

Теперь нужно объединить данные из двух групп:

- По `user_id` (один пользователь).
- По дате (`CAST(timestamp AS DATE)`), чтобы быть уверенными, что события произошли в один и тот же день.

**Что мы делаем:**

1. Используем `LEFT JOIN`, чтобы соединить таблицы.
2. Добавляем условия:
    - Время `page_load` должно быть раньше времени `page_exit`.
    - Дата `page_load` должна совпадать с датой `page_exit`.

```sql
user_page_session_times AS (
    SELECT
        l.user_id,
        CAST(page_load_timestamp AS DATE) AS page_load_date,
        CAST(page_exit_timestamp AS DATE) AS page_exit_date,
        page_exit_timestamp - page_load_timestamp AS session_time,
        RANK() OVER w AS relevant_session_rank
    FROM page_load_actions AS l
    LEFT JOIN page_exit_actions AS e
        USING (user_id)
    WHERE 
        page_load_timestamp <= page_exit_timestamp -- проверяем, что load < exit
        AND CAST(page_load_timestamp AS DATE) = CAST(page_exit_timestamp AS DATE) -- в один день
    WINDOW w AS (
        PARTITION BY user_id, CAST(page_load_timestamp AS DATE)
        ORDER BY page_load_timestamp DESC, page_exit_timestamp ASC
    )
)
```

---

#### **Шаг 4: Ранжируем данные**

Мы используем оконную функцию `RANK()`:

- **`PARTITION BY user_id, CAST(page_load_timestamp AS DATE)`**:
    - Разделяем данные для каждого пользователя и дня.
- **`ORDER BY page_load_timestamp DESC, page_exit_timestamp ASC`**:
    - Последний `page_load`.
    - Первый `page_exit`.

**Почему это нужно:**

- У нас могут быть несколько пар событий `page_load` и `page_exit` в один день.
- Ранжирование помогает выбрать только **последнюю сессию**.

---

#### **Шаг 5: Оставляем только релевантные сессии**

На этом этапе оставляем только сессии с `relevant_session_rank = 1`:

- Это последняя сессия за день для каждого пользователя.

```sql
SELECT
    user_id,
    AVG(session_time) AS avg_session_time
FROM user_page_session_times
WHERE relevant_session_rank = 1
GROUP BY user_id;
```

---

### **3. Почему это решение работает лучше**

1. **Ранжирование (`RANK`) вместо агрегаций (`MAX`/`MIN`):**
    
    - Более гибкий способ обработки данных.
    - Позволяет работать с несколькими записями на одну дату.
2. **Разделение данных (`WITH`):**
    
    - Делает логику шагов прозрачной.
    - Легко отлаживать или изменять логику на любом этапе.

---

### **4. Итог**

Это решение использует:

1. **Разделение данных (`page_load`, `page_exit`).**
2. **Фильтрацию (`page_load < page_exit`).**
3. **Ранжирование (`RANK()`) для выбора последней сессии.**
4. **Среднее время сессии для пользователей.**

Если всё ещё сложно, можно разобрать любой конкретный шаг более подробно! 😊