# **Код ETL-пайплайна: HDFS → Spark → Greenplum**

## **🔹 1. Читаем данные из HDFS в Spark**

from pyspark.sql import SparkSession

Создаём Spark-сессию
spark SparkSession.builder.appName("ETL_HDFS_to_GP").getOrCreate()

Читаем CSV-файл из HDFS
df = spark.read.option("header", "true").csv("hdfs://namenode:9000/data/sales.csv")

df.show(5)  # Проверяем первые 5 строк


✅ **Почему Spark?**

- Он умеет **распределённо читать и обрабатывать большие файлы** в HDFS
- Он может легко **обрабатывать CSV, JSON, Parquet**

---

## **🔹 2. Трансформация данных в Spark**

python

Копировать

`from pyspark.sql.functions import col, sum  # Фильтруем продажи только за 2024 год df = df.filter(col("sale_date") >= "2024-01-01")  # Агрегируем продажи по продуктам df_agg = df.groupBy("product_id").agg(sum("amount").alias("total_sales"))  df_agg.show(5)  # Проверяем результат`

✅ **Что здесь происходит?**

- Фильтруем **только нужные данные** (`sale_date >= "2024-01-01"`)
- Агрегируем **продажи по продуктам** (`SUM(amount) GROUP BY product_id`)

---

## **🔹 3. Сохраняем данные в локальный файл (для Greenplum `COPY`)**

python

Копировать

`output_path = "/tmp/sales_transformed.csv" df_agg.coalesce(1).write.option("header", "true").csv(output_path, mode="overwrite")`

✅ **Зачем `coalesce(1)`?**

- Spark **по умолчанию пишет много файлов** (по количеству партиций).
- `coalesce(1)` **объединяет всё в 1 CSV-файл** (оптимально для `COPY`).

---

## **🔹 4. Загружаем данные в Greenplum (`COPY`)**

bash

Копировать

`psql -h gp_host -U gp_user -d gp_database -c "     COPY sales_aggregated FROM '/tmp/sales_transformed.csv'     WITH CSV HEADER;"`

✅ **Когда использовать `COPY`?**

- Если **файл уже находится на сервере с Greenplum**.
- `COPY` **быстро загружает данные** (намного быстрее `INSERT`).

---

## **🔹 5. Альтернативный метод: `gpload` (если данные на удалённом сервере)**

### **📜 Конфигурация YAML-файла для `gpload`**

yaml

Копировать

`VERSION: 1.0.0.1 DATABASE: gp_database USER: gp_user HOST: gp_host PORT: 5432 TABLE: sales_aggregated MODE: INSERT FORMAT: CSV DELIMITER: ',' ERROR_LIMIT: 100 EXTERNAL: true LOGFILE: /tmp/gpload.log`

### **Запускаем `gpload`**

bash

Копировать

`gpload -f gpload_config.yml`

✅ **Когда использовать `gpload`?**

- Если данные находятся **не на сервере Greenplum**.
- Если нужно **массово загружать большие объёмы данных**.
- `gpload` **быстрее, чем `INSERT`, и безопаснее, чем `COPY` для больших данных**.