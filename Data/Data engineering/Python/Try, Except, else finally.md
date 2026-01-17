
class MyIterator:
    def __init__(self, max):
        self.max = max
        self.current = 0

    def __iter__(self):
        return self

    def __next__(self):
        if self.current < self.max:
            self.current += 1
            return self.current - 1
        else:
            raise StopIteration

# Использование итератора
my_iterator = MyIterator(5)
for value in my_iterator:
    print(value)
