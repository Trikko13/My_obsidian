

#### **1. Что такое SparkContext?**

`SparkContext` — это главный объект в Spark, который:

- Устанавливает связь между приложением и кластером.
- Управляет распределением задач и ресурсов.
- Создаёт и контролирует RDD (Resilient Distributed Dataset).

#### **Ключевые функции SparkContext:**

1. **Связь с кластером:**  
    Передаёт задачи узлам кластера (executor'ам) и координирует их выполнение.
2. **Управление RDD:**  
    Создаёт RDD, делит данные на партиции, распределяет их между узлами.
3. **Настройка работы:**  
    Устанавливает режим работы (`local`, `yarn`), задаёт ресурсы (память, ядра).

---

#### **Пример работы с SparkContext:**


`from pyspark import SparkContext`  

`Создаём SparkContext`
`sc = SparkContext("local", "Example App")` 

`Создаём RDD из списка` 
`data = [1, 2, 3, 4, 5]` 
`rdd = sc.parallelize(data)`  

`print(rdd.collect())  # [1, 2, 3, 4, 5]`  

`Останавливаем SparkContext` 

`sc.stop()`

---

#### **2. Что такое SparkSession?**

`SparkSession` появился в Spark 2.0 как универсальный интерфейс для работы:

- С RDD.
- С DataFrame и SQL.
- С другими компонентами Spark (MLlib, Streaming, Hive).

**SparkSession** автоматически включает:

1. **SparkContext:** для работы с RDD.
2. **SQLContext:** для выполнения SQL-запросов.
3. **HiveContext:** для интеграции с Hive.

---

#### **Функции SparkSession:**

1. **Управление RDD и DataFrame:**  
    Позволяет работать как с RDD, так и с таблицами (DataFrame).  
    Пример:
    

    `spark = SparkSession.builder.appName("DataFrame Example").getOrCreate() df = spark.read.csv("data.csv", header=True, inferSchema=True) df.show()`
    
2. **Интеграция с другими библиотеками:**  
    Например, для машинного обучения (MLlib) или потоковой обработки данных (Streaming).
    
3. **Упрощение работы:**  
    Вместо ручного создания `SparkContext` и `SQLContext`, достаточно одного объекта `SparkSession`.
    

---

#### **Пример работы с SparkSession:**


`from pyspark.sql import SparkSession`  

`Создаём SparkSession` 
`spark = SparkSession.builder.master("local").appName("Example").getOrCreate()`  

`Работа с DataFrame` 
`df = spark.read.csv("data.csv", header=True, inferSchema=True)` 
`df.show()`  

`Получение SparkContext из SparkSession` 
`sc = spark.sparkContext` 
`print(sc)`  

`Останавливаем SparkSession` 
`spark.stop()`

---

#### **3. Основные различия между SparkContext и SparkSession**

|**Функция**|**SparkContext**|**SparkSession**|
|---|---|---|
|**Назначение**|Управление RDD и кластером|Универсальный интерфейс (RDD, DataFrame, SQL)|
|**Современность**|Устаревший способ|Рекомендуется для использования с Spark 2.0+|
|**Работа с SQL**|Не поддерживает|Поддерживает|
|**Интеграция с Hive**|Не поддерживает|Полная поддержка|

---

#### **4. Как они связаны?**

- `SparkSession` автоматически создаёт и включает `SparkContext`.  
    Если вам нужен доступ к `SparkContext`, его можно получить через `spark.sparkContext`.

Пример:

`spark = SparkSession.builder.appName("Example").getOrCreate() sc = spark.sparkContext`

---

#### **5. Почему стоит использовать SparkSession?**

1. **Удобство:**  
    Один интерфейс для работы с RDD, DataFrame, SQL, Hive и другими компонентами.
2. **Совместимость:**  
    Современный стандарт, поддерживаемый Spark 2.0+.
3. **Автоматизация:**  
    Упрощает настройку и управление SparkContext.

---

#### **6. Пример работы с RDD через SparkSession**

Даже при работе с RDD стоит использовать SparkSession. Вот пример:


`from pyspark.sql import SparkSession`  

`Создаём SparkSession` 
`spark = SparkSession.builder.master("local").appName("RDD Example").getOrCreate()`  

`Получаем SparkContext` 
`sc = spark.sparkContext`  

`Создаём RDD` 
`data = [1, 2, 3, 4, 5]` 
`rdd = sc.parallelize(data)`  

`Применяем трансформацию map squared_rdd = rdd.map(lambda x: x**2) print(squared_rdd.collect())  # [1, 4, 9, 16, 25]`  
`Останавливаем SparkSession` 
`spark.stop()`

---

### **Итоговая логика работы:**

1. **SparkContext:**  
    Основной объект для работы с RDD и распределёнными вычислениями.
2. **SparkSession:**  
    Универсальный интерфейс для работы с RDD, DataFrame, SQL и другими компонентами.
3. **Лучше использовать SparkSession:**  
    Это упрощает настройку и совместимость с новыми версиями Spark.
