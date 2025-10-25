4. Salting (как «солить» данные)  
    Коротко: Добавляем псевдослучайную «соль» к горячему ключу, чтобы разнести его записи по нескольким партициям; потом «соль» убираем второй агрегацией/соединением.
    

Агрегация (двухфазная):

`from pyspark.sql import functions as F`  

`N = 16  # количество «корзин»`
`salted = (df.withColumn("salt", (F.rand(seed=42)*N).cast("int"))`             

`.groupBy("key", "salt").agg(F.sum("val").alias("part_sum"))) res = salted.groupBy("key").agg(F.sum("part_sum").alias("sum_val"))`

`Join (распределяем «горячие» ключи большой таблицы и размножаем малую):`

`# Большая: добавляем соль` 
`big_salted = df_big.withColumn("salt", (F.rand(seed=42)*N).cast("int"))`

`Малая: размножаем строки по соли (картеж N)` 
`from pyspark.sql.types import IntegerType` 
`seq = spark.range(N).withColumnRenamed("id", "salt")`
`small_salted = df_small.crossJoin(seq)`  

`res = big_salted.join(small_salted, on=["key","salt"])`

Примечание: salting увеличивает объём вычислений на малой стороне (мы её копируем N раз). Выбирайте N исходя из «горячести» ключей.