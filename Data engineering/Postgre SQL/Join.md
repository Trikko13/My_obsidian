https://habr.com/ru/articles/448072/  - часть 1
https://habr.com/ru/articles/450528/ - часть 2

Множество это уникальные элементы, а в таблице часто бывают одинаковые. Поэтому таблица, точно не множество.

Джоины это не пересечения множеств. Это все комбинации соединений строк  отфильтрованных по условию. Плюс ,если нет совпадений ,значения из левой таблицы если LEFT JOIN и из правой если RIGH JOIN. 

### Определения Join'ов

**Cross join**, классическое декартово произведение значений из двух таблиц:
```
SELECT t1.id, t2.id
FROM t1     
	CROSS JOIN t2
```
**Inner  join,** это тот же Cross join, но с условием соединения:
```
SELECT t1.id, t2.id
FROM t1     
	INNER JOIN t2        
		ON t1.id = t2.id
```
**Left Join** это тоже самое  что Inner Jooin но дополнительно добавляем null для строк из первой таблицы, для которой ничего не нашлось во второй:
```
SELECT t1.id, t2.id
FROM t1    
	LEFT JOIN t2        
		ON t1.id = t2.id
```
**Right Join** , очевидно что тот же Left но наоборот

```
SELECT t1.id, t2.id
FROM t1    
	RIGHT JOIN t2        
		ON t1.id = t2.id
```

Объяснение LEFT JOIN через Cross Join с объединением таблицы с условием NOT EXISTS
```
SELECT * FROM t1    
CROSS JOIN t2   
WHERE t1.id = t2.id

UNION ALL

SELECT t1.id, null   
	FROM t1   
	WHERE NOT EXISTS (
	        SELECT
	        FROM t2
	        WHERE t2.id = t1.id   
	)
```

## Условие ON

Удивительно, но по моим ощущениям 99% разработчиков считают, что в условии ON должен быть id из одной таблицы и id из второй. На самом деле там любое булево выражение.

Сложность задачи Джоинов - O(n) , где n - количество Таблиц к объединению. Поэтому если таблиц много ,то стоит применить СТЕ (https://habr.com/ru/articles/440576/)

Кстати, Еще маленький совет по производительности. Если нужно просто найти элементы в таблице, которых нет в другой таблице, то лучше использовать не 'LEFT JOIN… WHERE… IS NULL', а конструкцию EXISTS. Это и читабельнее, и быстрее.

**Алгоритм объединения INNER JOIN формируется следующим образом:**

- Сначала каждая строка первой таблицы сопоставляется с каждой строкой второй таблицы (происходит декартово произведение).
- Затем для каждой объединённой строки проверяется условие соединения, указанное после оператора `ON`.
- После этого все объединённые строки, для которых условие оказалось истинным, добавляются в результирующую таблицу.

**Алгоритм объединения LEFT JOIN формируется следующим образом:**
- Сначала каждая строка левой таблицы сопоставляется с каждой строкой правой таблицы (происходит декартово произведение).
- Затем для каждой объединённой строки проверяется условие соединения, указанное после оператора `ON`.
- После этого все объединённые строки, для которых условие оказалось истинным, добавляются в результирующую таблицу.
- Далее в результат добавляются те записи из левой таблицы (внимание: только из левой), для которых условие оказалось ложным и которые не вошли в соединение на предыдущем шаге. При этом для таких записей соответствующие поля из правой таблицы заполняются значениями `NULL`.



Полезные материалы:
1. Поиграть с запросами - https://sqlfiddle.com/postgresql/online-compiler?sqlFiddleLegacyID=15-2a20cc-5
2. 