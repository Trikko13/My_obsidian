Как я понял, return позволяет использовать полученный аргумент переменной в дальнейший математических операциях. Пример
### Основные отличия между `return` и `print`

1. **`return`** — возвращает значение из функции, чтобы его можно было использовать дальше.
2. **`print`** — выводит значение на экран, но **не передаёт его дальше в код**.

### Когда использовать `return`

- **Возвращать результат для дальнейшего использования**: Если функция должна вернуть значение, чтобы его можно было использовать где-то ещё в программе, нужно использовать `return`.
  
`def square(x): 
	`return x * x`
`#Использование функции в выражении
`result = square(5) + 10 
`print(result) # Выведет: 35`

### Когда использовать `print`

- **Для вывода информации пользователю**: Когда ты хочешь показать результат на экране, например, сообщение пользователю.
  **Пример**:
Предположим, у нас есть функция для вывода приветстви

`def greet(name):`
    `print(f"Hello, {name}!")`

`greet("Alice")  # Выведет: Hello, Alice!`


### Заключение

В большинстве случаев, особенно в вычислениях, лучше использовать `return`, так как это делает функцию более гибкой и полезной. `print` же лучше оставлять для вывода информации пользователю или отладки.