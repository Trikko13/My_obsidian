#### **Что такое Hadoop?**

Hadoop — это экосистема для обработки больших данных. Она включает несколько компонентов, среди которых два основных:

1. **HDFS (Hadoop Distributed File System):** Распределённая файловая система для хранения больших объёмов данных.
2. **MapReduce:** Модель вычислений для обработки данных в кластере.

#### **HDFS — что это?**

HDFS — распределённая файловая система, которая:

- Разделяет данные на блоки (по умолчанию **128 МБ**) и распределяет их по узлам кластера.
- Хранит данные с избыточностью (по умолчанию 3 копии) для защиты от сбоев.

**Почему блоки 128 МБ?**

- Это оптимальный баланс между количеством блоков и временем обработки. Блоки большего размера снижают накладные расходы на управление, но требуют больше памяти для обработки.

#### **Как Hadoop связан со Spark?**

- **HDFS в Spark:** Spark может читать данные из HDFS, обрабатывая их быстрее, чем MapReduce.
- **Замена MapReduce:** Spark более производителен благодаря распределённой памяти (RDD и DataFrame) и ленивым вычислениям. В отличие от Hadoop MapReduce, Spark сохраняет данные в памяти, что ускоряет вычисления.

#### **Spark без Hadoop:**

- Spark может работать без Hadoop (например, с локальными файлами, базами данных), но HDFS остаётся популярным источником данных в крупных системах.