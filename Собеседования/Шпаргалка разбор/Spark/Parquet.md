### **Parquet: Введение**

**Parquet** — это **колонко-ориентированный формат хранения данных**, который используется в больших данных из-за своей производительности и эффективности.

---

### **Особенности Parquet**

1. **Колонко-ориентированное хранение:**  
    Данные хранятся не построчно (как в CSV), а по столбцам. Это позволяет быстро читать только нужные столбцы.
    
2. **Сжатие:**
    
    - Parquet поддерживает эффективное сжатие данных, так как данные в столбцах часто однотипны.
    - Примеры алгоритмов: Snappy, Gzip.
3. **Поддержка схемы:**
    
    - Parquet хранит схему данных (названия и типы столбцов), что упрощает чтение.
4. **Оптимизация для аналитики:**
    
    - Чтение данных только из нужных столбцов (`Column Pruning`).
    - Фильтрация строк на уровне файлов (`Predicate Pushdown`).

---

### **Когда использовать Parquet**

1. **Большие данные:**  
    Если набор данных огромный, Parquet позволяет эффективно использовать дисковое пространство и ускоряет чтение.
    
2. **Аналитика:**  
    Для агрегаций и выборок по нескольким столбцам.
    
3. **Интеграция с Spark и Hadoop:**  
    Parquet — это родной формат для экосистемы Spark и Hadoop.
    

---

### **Пример работы с Parquet в Spark**

#### 1. Создание и сохранение данных в Parquet

`from pyspark.sql import` parkSession  

`# Создаём SparkSession` 
`spark = SparkSession.builder.master("local").appName("Parquet Example").getOrCreate()`  

`# Пример данных` 
`data = [("Alice", 34, "HR"), ("Bob", 23, "IT"), ("Cathy", 45, "Finance")]` 
`columns = ["name", "age", "department"]` 

`# Создаём DataFrame` 
`df = spark.createDataFrame(data, schema=columns)`  

`# Сохраняем DataFrame в формате Parquet df.write.mode("overwrite").parquet("people.parquet")`

---

#### 2. Чтение данных из Parquet

`# Читаем данные из Parquet` 
`parquet_df = spark.read.parquet("people.parquet")`  

`# Отображаем первые строки` 
`parquet_df.show()`

---

#### 3. Чтение только нужных столбцов

`# Читаем только столбец name parquet_df.select("name").show()`

Catalyst оптимизирует запрос и читает из файла только столбец `name`.

---

### **Predicate Pushdown в Parquet**

Predicate Pushdown — это механизм, который позволяет фильтровать данные на уровне файлов, ещё до их загрузки.

#### Пример:

`filtered_df = parquet_df.filter(parquet_df["age"] > 30) filtered_df.show()`

Если данные в Parquet-файле хранятся по блокам, Spark передаст фильтр `age > 30` в Parquet. Parquet вернёт только строки, которые соответствуют условию, не загружая остальные.

---

### **Best Practices для Parquet**

1. **Используй Parquet для аналитических данных.**
2. **Избегай переполненных столбцов.**  
    Если один столбец содержит много уникальных значений (например, `ID`), это снижает эффективность сжатия.
3. **Настрой сжатие:**
  
    `df.write.option("compression", "snappy").parquet("data.parquet")`
    
4. **Разбивай данные на партиции:**  
    Если ты работаешь с большими объёмами данных:
    `df.write.partitionBy("department").parquet("partitioned_data")`