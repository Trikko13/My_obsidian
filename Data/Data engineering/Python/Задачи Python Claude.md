```python
#1 — Easy Напиши функцию, которая принимает список чисел и возвращает словарь с тремя ключами: `min`, `max`, `avg`. Без использования `statistics` или `numpy`.


Input:  [4, 7, 2, 9, 1, 5]
Output: {'min': 1, 'max': 9, 'avg': 4.666...}

Input:  [42]
Output: {'min': 42, 'max': 42, 'avg': 42.0}

Input:  []
Output: None  (или raise ValueError — сам решай)
```

Решение:
```python
def describe(numbers: list ) ->  dict | None:
	if not numbers is None:
		return None
	return {
		"min" : min(numbers),
		"max" : max(numbers),
		"avg" : sum(numbers) / len(numbers)
	}

```


задача 2
```python
# Та же идея, чуть сложнее:
# Напиши функцию group_by_sign(numbers)
# которая принимает список чисел и возвращает словарь:
# {'positive': [...], 'negative': [...], 'zero': [...]}

# Input:  [3, -1, 0, 7, -4, 0, 2]
# Output: {'positive': [3, 7, 2], 'negative': [-1, -4], 'zero': [0, 0]}

# Input:  []
# Output: {'positive': [], 'negative': [], 'zero': []}
```

Решение:
```python
def group_by_sign(numbers: list) -> dict:
	result = {"positive": [], "negative": [], "zero"; []}
	
	for n in numbers:
		if n > 0:
			result['positive'].append(n)
		elif n < 0:
			result['negative'].append(n)
		else n = 0:
			result['zero'].append(n)
	return n
```

задача 3
```
# Напиши функцию flatten(nested) # которая разворачивает вложенный список на один уровень
 # Input: [[1, 2], [3, 4], [5]] 
 # Output: [1, 2, 3, 4, 5] 
 
 # Input: [[1, [2, 3]], [4]] 
 # Output: [1, [2, 3], 4] -- только один уровень, не рекурсивно! 
 
 # Input: [] 
 # Output: []
```

```python

def flatten(numbers: list) -> list:
	return [item for sublist in nested for item in sublist]
	
**Читается как:** "для каждого `sublist` в `nested`, возьми каждый `item` из `sublist`".

Разворачиваем мысленно:

python

# То же самое через обычный цикл:
result = []
for sublist in nested:
    for item in sublist:
        result.append(item)
```

List comprehension с двумя `for` — классический паттерн на собесах. Запомни структуру: `[item for sublist in outer for item in sublist]`. `[СОБЕС]`
```


