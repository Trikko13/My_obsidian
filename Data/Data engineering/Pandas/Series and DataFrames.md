Series в  Pandas похожа на столбец в таблице.
Это одномерный массив, содержащий данные любого типа.

Dataframes двумерная таблица, состоящая из строк и столбцов, как таблица в БД

Важное в Pandas 
Series - как колонка
DataFrame - как целая таблица

`import pandas as pd`  
  
`a = [1, 7, 2]`  
`calories = {'day1':420, 'day2' : 380, 'day3':390}`  
  
`myvar = pd.Series(a, index=['x','y','z'])`  
`mydir= pd.Series(calories)`  
  
  
`print(myvar)`  
`print(mydir['day3'])`  
  
  
`data = {`  
  `"calories": [420, 380, 390],`  
  `"duration": [50, 40, 45]`  
`}`  
  
  
`mydf = pd.DataFrame(data)`  
`print(mydf)`

**Pandas DataFrame** это двумерная структура данных, подобная двумерному массиву или таблице со строками и столбцами.

* Для выбора строки в Dataframe используется print(data.loc[index_num]). Данная команда вернет Pandas series (одномерный массив)