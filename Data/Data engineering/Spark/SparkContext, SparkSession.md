### **1. Что такое SparkContext?**

`SparkContext` — это главный объект в Spark, который:

- Связывает вашу программу с **кластером Spark**.
- Управляет распределённой обработкой данных.
- Создаёт **RDD** (Resilient Distributed Dataset) — базовую структуру данных Spark.

#### **Функции SparkContext:**

1. **Связь с кластером:**
    
    - SparkContext передаёт задачи на исполнение узлам кластера.
    - Например, в режиме `local` кластером будет ваш компьютер.
2. **Управление RDD:**
    
    - SparkContext создаёт и управляет RDD:
        - Разделяет данные на партиции.
        - Отправляет партиции на узлы для обработки.
        - Следит за выполнением задач.
3. **Настройка параметров:**  
    Через SparkContext вы задаёте:
    
    - Режим работы (`local`, `yarn`, `standalone`).
    - Количество используемых ресурсов (память, процессоры).
#### **Пример с SparkContext:**

`from pyspark import SparkContext`

`#Создаём SparkContext`
`sc = SparkContext("local", "Example App")`

`#Создаём RDD из списка`
`data = [1, 2, 3, 4, 5]`
`rdd = sc.parallelize(data)`

`print(rdd.collect())  # [1, 2, 3, 4, 5]`

`#Останавливаем SparkContext`
`sc.stop()`

### **2. Что такое SparkSession?**

`SparkSession` — это более современный и универсальный интерфейс, который появился в Spark 2.0. Он объединяет функциональность:

- **SparkContext**
- **SQLContext**
- **HiveContext**
- Другие компоненты для работы с Spark.

С помощью `SparkSession` вы можете:

- Работать с RDD.
- Использовать DataFrame и SQL.
- Чаще всего рекомендуется использовать именно `SparkSession`, а не вручную создавать `SparkContext`.

---

#### **Функции SparkSession:**

1. **Создание SparkContext:**
    
    - Когда вы создаёте `SparkSession`, Spark автоматически создаёт `SparkContext` (вы можете получить его через `spark.sparkContext`).
2. **Работа с DataFrame и SQL:**
    
    - `SparkSession` упрощает работу с таблицами и структурированными данными.
    - Пример:
             
        `from pyspark.sql import SparkSession  
        `spark = SparkSession.builder.appName("DataFrame Example").getOrCreate() 
        `df = spark.read.csv("path/to/file.csv", header=True, inferSchema=True) 
        `df.show()`
        
3. **Интеграция с другими компонентами:**
    
    - Hive, MLlib, Streaming — всё это доступно через `SparkSession`.
### **3. Разница между SparkContext и SparkSession**

|**Функция**|**SparkContext**|**SparkSession**|
|---|---|---|
|**Назначение**|Управление RDD и кластером|Универсальный интерфейс (RDD, DataFrame)|
|**Современность**|Старый способ работы|Рекомендуемый способ с Spark 2.0+|
|**Работа с SQL**|Не поддерживает|Поддерживает|
|**Интеграция с Hive**|Не поддерживает|Поддерживает|
### **4. Как они связаны?**

- `SparkSession` включает в себя `SparkContext`. Если вы создаёте `SparkSession`, вам не нужно вручную создавать `SparkContext` — он уже есть:

    `from pyspark.sql import SparkSession  
    `spark = SparkSession.builder.master("local").appName("Example").getOrCreate()  
    
	`Получение SparkContext из SparkSession sc = spark.sparkContext`
### **5. Почему нужно использовать SparkSession?**

1. **Удобство:**  
    Через один интерфейс (`SparkSession`) вы получаете доступ к RDD, DataFrame, SQL и другим компонентам.
2. **Совместимость:**  
    Это стандартный способ работы в новых версиях Spark.
3. **Меньше ошибок:**  
    Spark автоматически создаёт и управляет SparkContext, минимизируя ошибки, как в вашем примере.

---

### **6. Пример работы с RDD через SparkSession**

Даже если вы работаете с RDD, `SparkSession` — это лучший выбор:

`from pyspark.sql import SparkSession`  

`# Создаём SparkSession
`spark = SparkSession.builder.master("local").appName("RDD Example").getOrCreate()`  

`# Получаем SparkContext из SparkSession` 
`sc = spark.sparkContext`  

`# Работаем с RDD` 
`data = [1, 2, 3, 4, 5]` 
`rdd = sc.parallelize(data)` 
`squared_rdd = rdd.map(lambda x: x**2)` 
`print(squared_rdd.collect())  # [1, 4, 9, 16, 25]`  
`
`# Останавливаем SparkSession` 
`spark.stop()`

Давай разберём этот код построчно, чтобы стало понятно, что он делает:
---
### **Код:**


`from pyspark.sql import SparkSession`

1. **Импортируем SparkSession:**
    - Это основной объект, который управляет всей работой Spark.
    - Через `SparkSession` можно создавать RDD, DataFrame, запускать SQL-запросы и использовать другие функции Spark.

---
`spark = SparkSession.builder.master("local").appName("RDD Example").getOrCreate()`

2. **Создаём SparkSession:**
    - **`SparkSession.builder`**: Запускает процесс настройки `SparkSession`.
    - **`.master("local")`**: Указывает режим работы. Здесь `"local"` означает, что Spark работает на одном компьютере, а не на распределённом кластере.
    - **`.appName("RDD Example")`**: Указывает имя приложения Spark, которое будет отображаться в интерфейсе Spark UI.
    - **`.getOrCreate()`**: Либо создаёт новый `SparkSession`, либо возвращает существующий, если он уже запущен.

---
`sc = spark.sparkContext`

3. **Получаем SparkContext из SparkSession:**
    - `SparkSession` включает в себя `SparkContext`.
    - Здесь мы обращаемся к `SparkContext` для работы с RDD, так как RDD создаются через `SparkContext`.

---
`data = [1, 2, 3, 4, 5]`

4. **Создаём локальную коллекцию в Python:**
    - Это обычный Python-список, который содержит 5 чисел.
    - Пока это просто локальные данные, которые существуют в памяти Python.

---
`rdd = sc.parallelize(data)`

5. **Преобразуем локальные данные в RDD:**
    - **`sc.parallelize(data)`**: Разбивает данные на **партиции** и распределяет их для обработки в Spark.
    - Теперь `rdd` — это распределённая коллекция (RDD), которая готова к параллельной обработке.

---
`squared_rdd = rdd.map(lambda x: x**2)`

6. **Применяем трансформацию `map`:**
    - **`rdd.map(func)`**: Применяет функцию `func` к каждому элементу RDD.
    - **`lambda x: x**2`**: Это анонимная функция, которая возводит каждое число в квадрат.
    - Результатом является новый RDD (`squared_rdd`), который содержит числа `[1, 4, 9, 16, 25]`.

---

`print(squared_rdd.collect())`

7. **Действие `collect`:**
    - **`collect()`**: Собирает все элементы RDD обратно в локальный Python-список.
    - Здесь результат `[1, 4, 9, 16, 25]` выводится в консоль.

---

`spark.stop()`

8. **Останавливаем SparkSession:**
    - Это завершает работу приложения Spark.
    - Освобождаются все ресурсы, занятые Spark, включая драйвер и исполнителей.

---
### **Итоговая логика программы:**

1. Импортируется SparkSession.
2. Создаётся SparkSession в локальном режиме.
3. Получается SparkContext из SparkSession.
4. Локальный список преобразуется в RDD.
5. На RDD выполняется трансформация (`map`), возводя элементы в квадрат.
6. Результат собирается в Python-список и выводится.
7. Приложение Spark завершается.

