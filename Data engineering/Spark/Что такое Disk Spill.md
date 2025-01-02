**Disk Spill** происходит, когда **Executor** исчерпывает доступную память для обработки данных (например, для shuffle, join, или сортировки). Тогда Spark начинает временно записывать данные на диск.

### **Связь с Shuffle Memory:**

- **Shuffle Memory** — это часть **Execution Memory**, где данные хранятся во время операций shuffle.
- Если **Shuffle Memory** переполняется, данные записываются на диск (**Disk Spill**). Это значительно медленнее, чем работа в памяти, но позволяет продолжить выполнение задачи.
### **Категория памяти для Disk Spill:**

**Disk Spill** не относится к одной из стандартных категорий памяти (On-Heap, Off-Heap, Overhead). Это **внешняя память**, которая использует дисковое пространство узла.

- Для Disk Spill используются временные файлы на диске узла, где запущен Executor.
- Эти файлы создаются в директориях, указанных параметрами:
    `spark.local.dir`

### **Когда происходит Disk Spill?**

1. **Execution Memory заполнена:**
    
    - При выполнении shuffle или сортировки данных, если нет свободной памяти для временных вычислений.
2. **Storage Memory заполнена:**
    
    - Если Spark пытается закэшировать данные, но для них нет места.
3. **Off-Heap Memory заполнена:**
    
    - Если включена и активно используется, но данных больше, чем выделено в Off-Heap.
### **Пример:**

#### Если **Shuffle Memory** заполнена:


`from pyspark.sql import SparkSession  

`spark = SparkSession.builder \     .appName("DiskSpillExample") \     .config("spark.executor.memory", "2g") \       .config("spark.memory.fraction", "0.6") \     .config("spark.local.dir", "/tmp/spark-temp") \     .getOrCreate()  

`# Создаём DataFrame с большими данными 
`data = [(i, i % 1000) for i in range(10000000)] 
`df = spark.createDataFrame(data, ["id", "group"])  

`# Принудительно вызываем shuffle через groupBy 
`result = df.groupBy("group").count() 
`result.show()`