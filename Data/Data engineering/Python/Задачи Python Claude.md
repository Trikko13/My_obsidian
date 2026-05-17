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
