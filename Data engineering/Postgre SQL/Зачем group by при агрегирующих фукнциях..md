Когда ты используешь агрегатные функции, такие как `MAX()`, они работают по определённой логике — они сворачивают множество строк в одну строку для каждой **группы**. Если ты просто вызываешь `MAX()` или другую агрегатную функцию, ты просишь базу данных выполнить вычисления по какой-то группе данных, но без указания, как именно эти данные должны быть сгруппированы.

### Почему нужно использовать `GROUP BY`?

1. **Агрегатные функции (например, `MAX()`) сводят несколько строк в одну строку для каждой группы.**
    
    - Когда ты используешь функцию `MAX()`, она выбирает максимальное значение для определённой группы строк.
    - Если у тебя есть несколько колонок в `SELECT`, каждая колонка должна как-то взаимодействовать с функцией агрегации:
        - Либо должна быть частью **агрегатной функции** (`MAX()`, `SUM()`, и т.д.).
        - Либо должна быть частью **группировки** через `GROUP BY`, чтобы SQL понимал, как разбить строки на группы.
2. **Без `GROUP BY` SQL не знает, как агрегировать данные по полям, которые не включены в агрегатные функции.**
    
    - Допустим, у тебя есть таблица с несколькими колонками, например, `user_id` и `time` в таблице действий. Если ты хочешь найти максимальную временную отметку (`MAX(time)`) для каждого пользователя (`user_id`), ты должен указать, что агрегирование происходит **по пользователю** — это и делает `GROUP BY`.
    - Если не указать `GROUP BY`, SQL не понимает, как сгруппировать строки, к которым применяются агрегатные функции, и не сможет однозначно вычислить результат для каждой строки.