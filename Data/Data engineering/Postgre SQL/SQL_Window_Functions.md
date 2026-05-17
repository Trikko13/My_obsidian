# SQL Оконные функции — Шпаргалка

## Синтаксис
```sql
ФУНКЦИЯ(аргумент) OVER (
    PARTITION BY столбец   -- разбивка на группы
    ORDER BY столбец       -- порядок внутри группы
    ROWS BETWEEN ...       -- фрейм (опционально)
)
```

---

## Главное правило фрейма

| Ситуация | Писать |
|----------|--------|
| Есть `ORDER BY` | Пиши `ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW` явно |
| Нет `ORDER BY` | Не пиши ничего — по умолчанию вся группа |

---

## Паттерны

### 🔢 Накопительная сумма (running total)
```sql
SUM(amount) OVER (
    PARTITION BY user_id
    ORDER BY order_date
    ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
) AS running_total
```

### 📊 Агрегат по всей группе (не накопительный)
```sql
SUM(amount) OVER (PARTITION BY user_id) AS total_per_user
AVG(amount) OVER (PARTITION BY user_id) AS avg_per_user
```

### 📐 Доля от суммы группы
```sql
ROUND(amount / SUM(amount) OVER (PARTITION BY user_id), 2) AS pct_of_total
```

### 🏆 Ранжирование
```sql
-- С пропусками при совпадении (1,1,3)
RANK() OVER (PARTITION BY user_id ORDER BY amount DESC) AS rnk

-- Без пропусков (1,1,2)
DENSE_RANK() OVER (PARTITION BY user_id ORDER BY amount DESC) AS dense_rnk

-- Уникальный номер строки (1,2,3)
ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY order_date) AS rn
```

### ↔️ Смещение (предыдущее / следующее значение)
```sql
LAG(amount)  OVER (PARTITION BY user_id ORDER BY order_date) AS prev_amount
LEAD(amount) OVER (PARTITION BY user_id ORDER BY order_date) AS next_amount

-- С дефолтным значением вместо NULL
LAG(amount, 1, 0) OVER (PARTITION BY user_id ORDER BY order_date) AS prev_amount
```

### 🎯 Первое / последнее значение в группе
```sql
FIRST_VALUE(amount) OVER (PARTITION BY user_id ORDER BY order_date) AS first_order
LAST_VALUE(amount)  OVER (
    PARTITION BY user_id
    ORDER BY order_date
    ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
) AS last_order
-- ⚠️ LAST_VALUE требует явного фрейма, иначе вернёт текущую строку
```

### 📦 Перцентиль и процентиль
```sql
NTILE(4) OVER (PARTITION BY user_id ORDER BY amount) AS quartile  -- квартиль 1-4

PERCENT_RANK() OVER (ORDER BY amount)   -- позиция 0.0–1.0
CUME_DIST()    OVER (ORDER BY amount)   -- доля строк <= текущей
```

---

## Флаг сравнения с агрегатом
```sql
CASE WHEN amount > AVG(amount) OVER (PARTITION BY user_id) THEN 1 ELSE 0 END AS is_above_avg
```

---

## Где можно использовать оконные функции

| Клауза | Можно? |
|--------|--------|
| SELECT | ✅ |
| ORDER BY | ✅ |
| WHERE | ❌ |
| HAVING | ❌ |
| GROUP BY | ❌ |

**Обход ограничения** — вложенный запрос:
```sql
SELECT * FROM (
    SELECT amount,
           AVG(amount) OVER (PARTITION BY user_id) AS avg_amount
    FROM orders
) t
WHERE amount > avg_amount
```

---

## Частые ошибки на собесе [СОБЕС]

1. **Забыть аргумент**: `SUM()` вместо `SUM(amount)` — синтаксическая ошибка
2. **PARTITION BY не по той колонке**: `PARTITION BY order_id` когда нужен `user_id` — каждая строка станет своей группой
3. **LAST_VALUE без фрейма** — вернёт текущую строку, не последнюю
4. **RANK vs ROW_NUMBER** — при совпадении дат `RANK` даёт одинаковый ранг, `ROW_NUMBER` — произвольный порядок

---

## Шаблон ответа на собесе (2 минуты)

> "Оконные функции работают поверх результата запроса без сворачивания строк.
> Ключевые части: PARTITION BY задаёт группу, ORDER BY — порядок внутри группы,
> ROWS BETWEEN — фрейм вычисления. Использую SUM/AVG для агрегатов,
> RANK/ROW_NUMBER для ранжирования, LAG/LEAD для доступа к соседним строкам."
