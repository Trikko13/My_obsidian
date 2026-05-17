```sql
SQL задача — отличный выбор, это №1 по частоте на DE собесах. Задача #1 — Оконные функции (medium) Есть таблица `orders`:


order_id | user_id | amount | order_date
---------|---------|--------|------------
1        | 101     | 500    | 2024-01-03
2        | 101     | 200    | 2024-01-15
3        | 102     | 800    | 2024-01-05
4        | 101     | 300    | 2024-02-01
5        | 102     | 100    | 2024-02-10
6        | 103     | 600    | 2024-01-20

```

Задание: Для каждого заказа выведи:

- `user_id`, `order_id`, `amount`, `order_date`
- `running_total` — нарастающая сумма покупок пользователя (по дате)
- `order_rank` — ранг заказа у этого пользователя (от самого свежего, 1 = последний)
- `prev_amount` — сумма предыдущего заказа этого пользователя (NULL если первый) Решай в `SELECT ... FROM orders` — пиши прямо сюда.

Решение от Claude:

```sql
SELECT
    user_id,
    order_id,
    amount,
    order_date,

    -- нарастающая сумма по дате внутри каждого пользователя
    SUM(amount) OVER (
        PARTITION BY user_id
        ORDER BY order_date
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS running_total,

    -- ранг от свежего к старому (1 = последний заказ)
    RANK() OVER (
        PARTITION BY user_id
        ORDER BY order_date DESC
    ) AS order_rank,

    -- сумма предыдущего заказа
    LAG(amount) OVER (
        PARTITION BY user_id
        ORDER BY order_date
    ) AS prev_amount

FROM orders
ORDER BY user_id, order_date;
```


