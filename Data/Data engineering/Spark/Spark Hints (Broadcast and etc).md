3. Broadcast join  
    Коротко: Рассылаем малую таблицу на все executors и джойним локально — избегаем shuffle большой таблицы.
    

Как включить:

- Автоматически по порогу:
    
    `# типичный дефолт ~10 МБ; I cannot verify this для вашей сборки spark.conf.set("spark.sql.autoBroadcastJoinThreshold", 256*1024*1024)  # пример 256MB`
    
- Явно (лучше для собеса показать оба синтаксиса):
    
    `from pyspark.sql.functions import broadcast df_big.join(broadcast(df_small), "key")  spark.sql("""SELECT /*+ BROADCAST(s) */ *               FROM big b JOIN small s ON b.key = s.key""")`
    

Про «максимальный размер для броадкаста 8 ГБ»:

- 10mb default, 8Gb - max.
    
- Практический потолок задаётся памятью драйвера/экзекьюторов, сериализацией и фрагментацией broadcast-блоков. Реалистично — десятки/сотни МБ, реже единицы ГБ. Чем больше — тем выше риск OOM/долгой раздачи.

7. Хинты в Spark SQL (что перечислить)
    

- Управление join:
    
    - `/*+ BROADCAST(t) */`, синонимы: `MAPJOIN`, `BROADCASTJOIN`.
        
    - `/*+ SHUFFLE_HASH(t1, t2) */`, `/*+ SHUFFLE_MERGE(t1, t2) */` (aka Sort Merge).
        
    - `/*+ MERGE(t1, t2) */` — форсировать SMJ.
        
    - `/*+ SHUFFLE_REPLICATE_NL(t1, t2) */` — для nested loop (осторожно!).
        
- Партиционирование/коалес:
    
    - `/*+ COALESCE(n) */`, `/*+ REPARTITION(n) */`,
        
    - `/*+ REPARTITION_BY_RANGE(n, col1, col2) */`.
        
- Skew/AQE:
    
    - В Spark 3 с AQE — автоматический skew-join; вручную — через salting.