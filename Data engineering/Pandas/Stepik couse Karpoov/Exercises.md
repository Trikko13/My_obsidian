1. инфа о колонках и строках - student_performance.shape 
2. тип полей - student_performance.describe 
3.  Аггрегация - student_performance.groupby('gender').aggregate({'writing score' : 'count'})
4. # отберем 5 строк и 3 переменные через iloc (integer_location)
*student_performance.iloc[0:5, 0:3]*
5. отдельные колонки и строки
*student_performance.iloc[[0, 3, 10], [0, 5, -1]]*
6. # отобрать строки используя лейблы через location(loc)
student_performance_with_names.loc[['Cersei', 'Joffrey'], ['gender', 'writing score']]
7. # это даст одинаковый результат
student_performance_with_names.iloc[:,0]
student_performance_with_names['gender']
student_performance_with_names.gender
8.# Первый способ - получим Pandas Series
#student_performance_with_names['gender']
#Второй - Pandas Dataframe
student_performance_with_names[['gender']]

