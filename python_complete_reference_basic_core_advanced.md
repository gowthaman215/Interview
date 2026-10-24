# Python Programming — Complete Reference (Basic, Core, Advanced)

## Introduction

Python is a high-level, interpreted, dynamically typed language known for:
- Readability
- Rapid development
- Rich standard library
- Strong ecosystem

Python is widely used in:
- Automation
- Backend services
- Data processing
- Scripting alongside C/C++
- DevOps and tooling

---

## BASIC PYTHON CONCEPTS

## Variables and Data Types

Python variables are dynamically typed.

```python
x = 10
y = 3.14
name = "Python"
is_valid = True
```

Common built-in types:
- int
- float
- str
- bool
- None

---

## Input and Output

```python
name = input("Enter name: ")
print("Hello", name)
```

---

## Operators

### Arithmetic Operators

```python
a = 10 + 5
b = 10 / 3
c = 10 // 3
d = 10 % 3
```

---

### Comparison Operators

```python
x > y
x == y
x != y
```

---

### Logical Operators

```python
if x > 0 and y > 0:
    print("Positive")
```

---

## Control Flow

### if / elif / else

```python
if x > 0:
    print("Positive")
elif x < 0:
    print("Negative")
else:
    print("Zero")
```

---

### for Loop

```python
for i in range(5):
    print(i)
```

---

### while Loop

```python
count = 0
while count < 5:
    count += 1
```

---

## Functions

```python
def add(a, b):
    return a + b
```

Key points:
- Functions are first-class objects
- Can be passed as arguments

---

## CORE PYTHON CONCEPTS

## Data Structures

### List

```python
nums = [1, 2, 3]
nums.append(4)
```

---

### Tuple

```python
point = (10, 20)
```

Immutable sequence.

---

### Dictionary

```python
user = {"name": "Alice", "age": 30}
print(user["name"])
```

---

### Set

```python
s = {1, 2, 3}
s.add(4)
```

---

## String Handling

```python
text = "hello python"
print(text.upper())
print(text.split())
```

---

## List Comprehensions

```python
squares = [x*x for x in range(5)]
```

Concise and efficient.

---

## Exception Handling

```python
try:
    x = int("abc")
except ValueError:
    print("Conversion failed")
finally:
    print("Done")
```

---

## File Handling

```python
with open("data.txt", "w") as f:
    f.write("Hello")
```

`with` ensures automatic cleanup.

---

## Modules and Packages

```python
import math
print(math.sqrt(16))
```

Custom module:
```python
import mymodule
```

---

## Object-Oriented Programming (OOP)

## Classes and Objects

```python
class Car:
    def __init__(self, brand):
        self.brand = brand

    def drive(self):
        print("Driving", self.brand)
```

---

## Inheritance

```python
class ElectricCar(Car):
    pass
```

---

## Encapsulation

```python
class Account:
    def __init__(self):
        self.__balance = 0
```

Double underscore makes attribute private (name mangling).

---

## Polymorphism

```python
def start(car):
    car.drive()
```

Duck typing:
> “If it quacks like a duck, it’s a duck.”

---

## ADVANCED PYTHON CONCEPTS

## Iterators and Generators

### Iterator

```python
nums = [1,2,3]
it = iter(nums)
print(next(it))
```

---

### Generator

```python
def count_up(n):
    for i in range(n):
        yield i
```

Generators are memory efficient.

---

## Decorators

Decorators modify functions.

```python
def my_decorator(func):
    def wrapper():
        print("Before")
        func()
        print("After")
    return wrapper

@my_decorator
def say_hello():
    print("Hello")
```

---

## Lambda Functions

```python
add = lambda a, b: a + b
```

Used for short, inline functions.

---

## *args and **kwargs

```python
def func(*args, **kwargs):
    print(args)
    print(kwargs)
```

Used for flexible APIs.

---

## Context Managers

```python
class MyContext:
    def __enter__(self):
        print("Enter")
    def __exit__(self, exc_type, exc, tb):
        print("Exit")
```

Used with `with` statement.

---

## Memory Management

Python uses:
- Reference counting
- Garbage collection

```python
import gc
gc.collect()
```

---

## Shallow vs Deep Copy

```python
import copy
a = [1, [2,3]]
b = copy.copy(a)
c = copy.deepcopy(a)
```

---

## Multithreading vs Multiprocessing

### Threading (GIL)

```python
import threading

def task():
    print("Working")

t = threading.Thread(target=task)
t.start()
```

Threads share memory but are limited by GIL.

---

### Multiprocessing

```python
from multiprocessing import Process

def task():
    print("Working")

p = Process(target=task)
p.start()
```

Uses multiple CPU cores.

---

## Async Programming (async / await)

```python
import asyncio

async def main():
    await asyncio.sleep(1)
    print("Done")

asyncio.run(main())
```

Used for I/O-bound concurrency.

---

## Virtual Environments

```bash
python -m venv venv
source venv/bin/activate
```

Isolates dependencies.

---

## Python vs C++ (Quick Comparison)

| Python | C++ |
|-----|-----|
| Interpreted | Compiled |
| Dynamic typing | Static typing |
| Slower | Faster |
| High productivity | High performance |

Python complements C++ well.

---

## Common Interview Questions

- Difference between list and tuple?
- What is GIL?
- What are decorators?
- Generator vs list?
- Shallow vs deep copy?

---

## Best Practices

- Use virtual environments
- Write readable code
- Use standard libraries
- Handle exceptions properly
- Avoid premature optimization

---

## One-Line Takeaway

Python is a powerful, expressive language that prioritizes readability and productivity, while still offering advanced features for scalable and concurrent applications.
