Разберём всё по порядку: что такое **ACID**, а также рассмотрим **аномалии**, возникающие при выполнении транзакций, с подробными примерами и комментариями.

---

## ACID: Основные принципы

1. **Атомарность (Atomicity):**
    
    - Транзакция либо выполняется **целиком**, либо не выполняется вообще.
    - Если одна часть транзакции не удалась, система должна откатить все изменения.
    - **Пример:**
        - Банковский перевод: списание со счета A и зачисление на счет B должны быть выполнены обе операции, либо ни одна из них. Если списание успешно, но зачисление не удалось, транзакция откатывается.
2. **Согласованность (Consistency):**
    
    - После выполнения транзакции данные в базе должны оставаться в **валидном состоянии**, соблюдая все ограничения и правила (например, целостность связей, ограничения CHECK и т.д.).
    - **Пример:**
        - Если сумма денег на счетах A и B должна оставаться постоянной, то после транзакции это условие должно быть выполнено.
3. **Изолированность (Isolation):**
    
    - Параллельно выполняющиеся транзакции не должны **вмешиваться друг в друга** и порождать некорректные состояния данных.
    - **Режимы изоляции:**
        - Read Uncommitted
        - Read Committed
        - Repeatable Read
        - Serializable (самый строгий уровень).
    - Подробнее про изоляцию и аномалии — ниже.
4. **Устойчивость (Durability):**
    
    - Если транзакция завершена успешно, её результат должен быть **сохранён навсегда**, даже в случае сбоя системы.
    - **Пример:**
        - Если вы перевели деньги, банк гарантирует, что после завершения операции данные не потеряются, даже если сервер выйдет из строя.

---

## Аномалии при выполнении транзакций

Теперь разберём **аномалии** и их связь с режимами изоляции, чтобы устранить путаницу.

---

### 1. Потерянные обновления (**Lost Updates**)

**Описание:**

- Две транзакции параллельно обновляют одни и те же данные, и результат первой транзакции теряется, так как он перезаписывается второй.

**Пример:**

- Транзакция 1: Увеличить количество товара `A` на складе на 10.
- Транзакция 2: Увеличить количество товара `A` на складе на 5.

Ход выполнения:

1. Транзакция 1 читает текущее количество товара `A` (50).
2. Транзакция 2 читает то же значение (50).
3. Транзакция 1 добавляет 10 и записывает результат (60).
4. Транзакция 2 добавляет 5 и записывает результат (55), **перезаписывая** результат Транзакции 1.

**Результат:**

- Итоговое значение товара `A` будет **55 вместо 65**.

**Решение:**

- Использование блокировок (например, `FOR UPDATE`) или уровня изоляции `Serializable`.

---

### 2. Грязное чтение (**Dirty Read**)

**Описание:**

- Транзакция читает изменения, внесённые другой транзакцией, которая ещё не завершилась. Если та транзакция откатится, данные будут неверными.

**Пример:**

- Транзакция 1: Изменить баланс счёта на `1000` (но не зафиксировать).
- Транзакция 2: Прочитать баланс счёта (1000).

Ход выполнения:

1. Транзакция 1 обновляет баланс счёта с 500 на 1000, но **не завершает** транзакцию.
2. Транзакция 2 читает баланс (1000) и строит отчёт.
3. Транзакция 1 откатывается, и баланс возвращается к 500.

**Результат:**

- Отчёт Транзакции 2 содержит **невалидные данные**.

**Решение:**

- Уровень изоляции `Read Committed` или выше предотвращает грязное чтение.

---

### 3. Неповторяющееся чтение (**Non-Repeatable Read**)

**Описание:**

- Транзакция повторно читает те же данные, но получает **разные результаты**, так как другая транзакция внесла изменения.

**Пример:**

- Транзакция 1: Прочитать баланс счёта.
- Транзакция 2: Изменить баланс счёта и зафиксировать изменения.
- Транзакция 1: Прочитать баланс счёта снова.

Ход выполнения:

1. Транзакция 1 читает баланс (500).
2. Транзакция 2 обновляет баланс (500 → 1000) и завершает транзакцию.
3. Транзакция 1 читает баланс снова и получает 1000.

**Результат:**

- Баланс изменился между чтениями, что **может нарушить логику Транзакции 1**.

**Решение:**

- Уровень изоляции `Repeatable Read` или выше.

---

### 4. Фантомное чтение (**Phantom Read**)

**Описание:**

- Транзакция повторно выполняет запрос на чтение данных с определённым условием (`WHERE`), но между запросами другая транзакция добавляет или удаляет строки, которые соответствуют этому условию.

**Пример:**

- Транзакция 1: Прочитать список всех заказов с суммой > 100.
- Транзакция 2: Вставить новый заказ с суммой > 100 и зафиксировать изменения.
- Транзакция 1: Прочитать список заказов снова.

Ход выполнения:

1. Транзакция 1 читает список заказов: `Order 1`, `Order 2`.
2. Транзакция 2 добавляет новый `Order 3` с суммой 150 и завершает транзакцию.
3. Транзакция 1 повторно читает список заказов: `Order 1`, `Order 2`, `Order 3`.

**Результат:**

- Во втором чтении появляются **новые строки**, которых не было в первом.

**Решение:**

- Уровень изоляции `Serializable`, который предотвращает подобные изменения.

---

## Различие между неповторяющимся и фантомным чтением

- **Неповторяющееся чтение**:  
    Изменяются **данные уже существующих строк** между чтениями.  
    Пример: баланс счёта одного клиента изменился.
    
- **Фантомное чтение**:  
    Изменяется **набор строк** между чтениями (добавляются/удаляются новые строки).  
    Пример: в таблицу заказов добавился новый заказ.
    

**Итого:**

- Неповторяющееся чтение — изменение **существующих строк**.
- Фантомное чтение — появление **новых строк** или исчезновение существующих строк.

---

## Уровни изоляции и аномалии

|Уровень изоляции|Грязное чтение|Неповторяющееся чтение|Фантомное чтение|
|---|---|---|---|
|**Read Uncommitted**|✅ Допустимо|✅ Допустимо|✅ Допустимо|
|**Read Committed**|❌ Нет|✅ Допустимо|✅ Допустимо|
|**Repeatable Read**|❌ Нет|❌ Нет|✅ Допустимо|
|**Serializable**|❌ Нет|❌ Нет|❌ Нет|

---

## Заключение

1. **Неповторяющееся чтение**: фокусируется на изменении данных существующих строк.
2. **Фантомное чтение**: добавляются/удаляются новые строки, соответствующие условиям выборки.
3. Для предотвращения всех аномалий используйте уровень изоляции `Serializable`, но он медленный из-за своей строгости. Выбор уровня изоляции зависит от бизнес-требований к данным.