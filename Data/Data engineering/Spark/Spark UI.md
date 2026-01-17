6. Быстрый чек-лист (по вкладкам):
    

Jobs:

- Какие jobs долгие/failed; соответствие actions. Timeline — где простаиваем.
    

Stages (главная):

- Duration (Max vs Median), Shuffle Read/Write, Spill(ons), GC Time.
    
- Timeline: есть ли «висячая» таска; Locality Level; Scheduler Delay.
    

Stage → Tasks:

- Перцентили по Duration/Shuffle/GC; ищем перекос, сетевые задержки (Shuffle Fetch Wait), сериализацию.
    

SQL:

- Exchange (границы shuffle); WholeStageCodegen блоки (сопоставляю со stage’ами).
    
- number of output rows на узлах плана (где «раздулись» данные).
    
- Вид join (Broadcast/SortMerge/ShuffleHash), есть ли adaptive coalesce.
    

Executors:

- Равномерность Tasks, Shuffle Read/Write, GC Time; Failed Tasks; «чёрный список».
    
- Logs/Thread Dump — если есть падения/зависания.
    

Storage:

- Закешировалось ли (Fraction Cached), где и сколько памяти съело.