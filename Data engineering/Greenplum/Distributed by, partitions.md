**Greenplum работает на нескольких узлах, и важно правильно распределять данные между ними!**

🔹 **Пример создания таблицы с `DISTRIBUTED BY`:**


`CREATE TABLE sales (     
	id SERIAL PRIMARY KEY,     
	product_id INT,     
	amount DECIMAL(10,2),     
	sale_date DATE 
) DISTRIBUTED BY (product_id); -- Ключ распределения`

📌 **Что тут важно?**

- `DISTRIBUTED BY (product_id)` → **записи с одинаковым `product_id` будут на одном узле**.
- Это снижает межузловой трафик при `JOIN`, `GROUP BY`, `COUNT(*)`.

✅ **Как выбрать ключ `DISTRIBUTED BY`?**

- Должен быть **равномерно распределён** (чтобы узлы работали равномерно).
- НЕ должен быть **малоразмерным** (`DISTRIBUTED BY (country_code)` ❌, если всего 5 стран → дисбаланс).
- Для JOIN-ов **лучше выбирать ключ, который часто используется в связках** (`user_id`, `order_id`).

🔹 **Как проверить, равномерно ли распределены данные?**

`SELECT gp_segment_id, COUNT(*)  FROM sales  GROUP BY gp_segment_id;`


# **`EXPLAIN ANALYZE` (анализ и оптимизация запросов)**

📌 **Как проверить, насколько эффективно работает запрос?**

🔹 **Используем `EXPLAIN ANALYZE`:**

`EXPLAIN ANALYZE  
`SELECT product_id, SUM(amount)  
`FROM sales  
`GROUP BY product_id;`

📌 **Вывод покажет:**  
✅ Время выполнения (`Total runtime`)  
✅ Использование индексов  
✅ Есть ли **BroadCast Motion** (если есть, значит `JOIN` неоптимален)

🔹 **Что такое `Motion`?**

- `Broadcast Motion` → **копирует всю таблицу на все узлы** (очень медленно!)
- `Redistribute Motion` → **перераспределяет данные между узлами** (медленно, но лучше, чем Broadcast)
- **Оптимальный вариант** → **без Motion**, когда данные уже лежат правильно

📌 **Как уменьшить `Motion`?**  
✅ **Выбирать правильный `DISTRIBUTED BY`**, чтобы `JOIN` не требовал пересылки данных.  
✅ Использовать **`ANALYZE`** для обновления статистики таблицы:


`ANALYZE sales;`

✅ Создавать **индексы** (`CREATE INDEX ON sales(product_id);`)


# **`PARTITION BY` (партиционирование таблиц)**

📌 **Партиционирование = логическое разбиение таблицы на части (партиции) для ускорения работы.**

🔹 **Пример партиционированной таблицы по дате:**
 

`CREATE TABLE sales (     
	`id SERIAL PRIMARY KEY,     
	`product_id INT,     
	`amount DECIMAL(10,2),     
	`sale_date DATE 
`) PARTITION BY RANGE (sale_date) ( 
	`PARTITION sales_2023 START ('2023-01-01') END ('2023-12-31'),     
	`PARTITION sales_2024 START ('2024-01-01') END ('2024-12-31') 
`);`

📌 **Почему это ускоряет работу?**

- Когда запрос выполняется с `WHERE sale_date BETWEEN '2024-01-01' AND '2024-01-31'`, Greenplum **читает только нужную партицию** (Partition Pruning).

✅ **Когда использовать партиционирование?**

- Если данные **имеют естественное логическое деление** (`date`, `region_id`).
- Если есть **очень большие таблицы** (миллионы/миллиарды строк).

# **Как загружать данные в Greenplum? (`COPY`, `INSERT`, `gpload`)**

✅ **Способы загрузки данных в Greenplum:**

🔹 **1. `COPY` (самый быстрый метод)**

`COPY sales FROM '/data/sales.csv' WITH DELIMITER ',' CSV HEADER;`

📌 **Почему это быстро?**

- `COPY` загружает данные **параллельно** во все сегменты Greenplum.

---

🔹 **2. `INSERT` (медленно, подходит для малых данных)**

`INSERT INTO sales (product_id, amount, sale_date) VALUES (1, 100.50, '2024-02-10');`

📌 **Когда использовать?**

- Если вставляются **единичные строки** (не подходит для массовой загрузки).

---

🔹 **3. `gpload` (ETL-инструмент, загружает данные массово)**

- Используется в продакшене **для загрузки больших объёмов данных**.
- Использует файлы **YAML-конфигурации** для настройки загрузки.

📌 **Пример конфигурации `gpload` (YAML-файл):**

yaml

Копировать

`VERSION: 1.0.0.1 DATABASE: my_database USER: my_user HOST: gp_master PORT: 5432 TABLE: sales MODE: INSERT FORMAT: CSV DELIMITER: ',' ERROR_LIMIT: 100`

📌 **Запуск gpload:**

bash

Копировать

`gpload -f gpload_config.yml`

✅ **Когда использовать?**

- Когда нужно **загружать гигабайты данных** из файлов (быстро и эффективно).