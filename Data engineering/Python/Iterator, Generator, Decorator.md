Итератор - обьект который поддерживает пошаговый перебор элементов через методы 

class MyIterator:
    def __init__(self, start, end):
        self.current = start
        self.end = end

    def __iter__(self):
        return self  # Итератор сам возвращает себя

    def __next__(self):
        if self.current >= self.end:
            raise StopIteration  # Останавливаем итерацию
        value = self.current
        self.current += 1
        return value

#Используем итератор
iterator = MyIterator(1, 5)
for num in iterator:
    print(num)  # Выведет: 1 2 3 4

🔹 **Когда использовать?**

- Когда нужно **оптимизировать память**, например, **читать файлы построчно** (`open().readline()`).
- Когда объект **не должен хранить в памяти все элементы** (например, бесконечные последовательности).

## **2. Генераторы (`Generator`)**

🔹 **Что это?**  
Генератор — это **функция, которая использует `yield` вместо `return`**.

- Он **запоминает своё состояние** между вызовами.
- Позволяет **создавать последовательности элементо**


**Как работает?**

- `yield` **замораживает** выполнение функции и **возвращает значение**.
- `next(generator)` **возобновляет выполнение** после `yield`.
def my_generator():
    yield 1
    yield 2
    yield 3

gen = my_generator()
print(next(gen))  # 1
print(next(gen))  # 2
print(next(gen))  # 3
🔹 **Пример: Генератор бесконечной последовательности**


def infinite_numbers():
    num = 1
    while True:
        yield num
        num += 1

gen = infinite_numbers()
print(next(gen))  # 1
print(next(gen))  # 2
print(next(gen))  # 3


## **3. Декораторы (`Decorator`)**

🔹 **Что это?**  
Декоратор — это **функция, которая принимает другую функцию и расширяет её функционал** без изменения кода.

🔹 **Как работает?**

- Декоратор оборачивает функцию **в другую функцию**.
- Используется `@decorator_name` перед функцией.
`def logger(func):`
    `def wrapper(*args, **kwargs):`
        `print(f"Вызов функции {func.__name__} с аргументами {args}")`
        `result = func(*args, **kwargs)`
        `print(f"Результат: {result}")`
        `return result`
    `return wrapper`

`@logger`
`def add(a, b):`
    `return a + b`

`print(add(3, 5))`  
`# Вывод:`
`# Вызов функции add с аргументами (3, 5)`
`# Результат: 8`
`# 8`
