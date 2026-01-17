2. Как уменьшить shuffle  
    Коротко: уменьшаем объём и количество shuffle-границ, делаем вычисления «на месте».
    

Техники:

- Ранние фильтры/проекции: отбрасывать столбцы/строки ДО широких операций (join/agg/window).
    
- Broadcast малой таблицы (см. ниже), чтобы избежать shuffle на join.
    
- Map-side aggregate: использовать агрегаты, которые «сжимаются» до shuffle (в RDD — reduceByKey вместо groupByKey; в SQL планах — partial aggregate).
    
- Совместимое партиционирование: repartition по join-ключу, одинаковое numPartitions для обеих сторон; bucketing по одному ключу и одинаковому числу бакетов — Spark может сделать bucketed sort-merge без полной перестановки.
    
- AQE: автоматическое coalesce после shuffle, динамический выбор join.
    
    `spark.conf.set("spark.sql.adaptive.coalescePartitions.enabled", "true")`
    
- Избегать «мелких файлов»: объединять input; уменьшать число shuffle-partitions разумно:
    
    `spark.conf.set("spark.sql.shuffle.partitions", "N")  # под размер кластера/объём`