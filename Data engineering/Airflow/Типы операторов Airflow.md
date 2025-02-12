✅ **Основные операторы в Airflow:**

- **`PythonOperator`** → выполняет Python-код.
- **`BashOperator`** → выполняет команду Bash (например, `ls`, `hdfs dfs -ls /data`).
- **`PostgresOperator`** → выполняет SQL-запросы в Postgres/Greenplum.
- **`HiveOperator`** → выполняет Hive-запросы в Hadoop.
- **`SparkSubmitOperator`** → запускает Spark-задачи.
- **`DummyOperator`** → используется как пустой таск-заполнитель (например, для группировки).