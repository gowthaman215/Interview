= Python Programming — Complete Reference (Basic, Core, Advanced)
:toc:
:toclevels: 4
:sectnums:
:source-highlighter: coderay

== Introduction

Python is a high-level, interpreted, dynamically typed language known for:
* Readability
* Rapid development
* Rich standard library
* Strong ecosystem

Python is widely used in:
* Automation
* Backend services
* Data processing
* Scripting alongside C/C++
* DevOps and tooling

---

== BASIC PYTHON CONCEPTS

== Variables and Data Types

Python variables are dynamically typed.

[source,python]
----
x = 10
y = 3.14
name = "Python"
is_valid = True
----

Common built-in types:
* int
* float
* str
* bool
* None

---

== Input and Output

[source,python]
----
name = input("Enter name: ")
print("Hello", name)
----

---

== Operators

=== Arithmetic Operators

[source,python]
----
a = 10 + 5
b = 10 / 3
c = 10 // 3
d = 10 % 3
----

---

=== Comparison Operators

[source,python]
----
x > y
x == y
x != y
----

---

=== Logical Operators

[source,python]
----
if x > 0 and y > 0:
    print("Positive")
----

---

== Control Flow

=== if / elif / else

[source,python]
----
if x > 0:
    print("Positive")
elif x < 0:
    print("Negative")
else:
    print("Zero")
----

---

=== for Loop

[source,python]
----
for i in range(5):
    print(i)
----

---

=== while Loop

[source,python]
----
count = 0
while count < 5:
    count += 1
----

---

== Functions

[source,python]
----
def add(a, b):
    return a + b
----

Key points:
* Functions are first-class objects
* Can be passed as arguments

---

== CORE PYTHON CONCEPTS

== Data Structures

=== List

[source,python]
----
nums = [1, 2, 3]
nums.append(4)
----

---

=== Tuple

[source,python]
----
point = (10, 20)
----

Immutable sequence.

---

=== Dictionary

[source,python]
----
user = {"name": "Alice", "age": 30}
print(user["name"])
----

---

=== Set

[source,python]
----
s = {1, 2, 3}
s.add(4)
----

---

== String Handling

[source,python]
----
text = "hello python"
print(text.upper())
print(text.split())
----

---

== List Comprehensions

[source,python]
----
squares = [x*x for x in range(5)]
----

Concise and efficient.

---

== Exception Handling

[source,python]
----
try:
    x = int("abc")
except ValueError:
    print("Conversion failed")
finally:
    print("Done")
----

---

== File Handling

[source,python]
----
with open("data.txt", "w") as f:
    f.write("Hello")
----

`with` ensures automatic cleanup.

---

== Modules and Packages

[source,python]
----
import math
print(math.sqrt(16))
----

Custom module:
[source,python]
----
import mymodule
----

---

== Object-Oriented Programming (OOP)

== Classes and Objects

[source,python]
----
class Car:
    def __init__(self, brand):
        self.brand = brand

    def drive(self):
        print("Driving", self.brand)
----

---

== Inheritance

[source,python]
----
class ElectricCar(Car):
    pass
----

---

== Encapsulation

[source,python]
----
class Account:
    def __init__(self):
        self.__balance = 0
----

Double underscore makes attribute private (name mangling).

---

== Polymorphism

[source,python]
----
def start(car):
    car.drive()
----

Duck typing:
> “If it quacks like a duck, it’s a duck.”

---

== ADVANCED PYTHON CONCEPTS

== Iterators and Generators

=== Iterator

[source,python]
----
nums = [1,2,3]
it = iter(nums)
print(next(it))
----

---

=== Generator

[source,python]
----
def count_up(n):
    for i in range(n):
        yield i
----

Generators are memory efficient.

---

== Decorators

Decorators modify functions.

[source,python]
----
def my_decorator(func):
    def wrapper():
        print("Before")
        func()
        print("After")
    return wrapper

@my_decorator
def say_hello():
    print("Hello")
----

---

== Lambda Functions

[source,python]
----
add = lambda a, b: a + b
----

Used for short, inline functions.

---

== *args and **kwargs

[source,python]
----
def func(*args, **kwargs):
    print(args)
    print(kwargs)
----

Used for flexible APIs.

---

== Context Managers

[source,python]
----
class MyContext:
    def __enter__(self):
        print("Enter")
    def __exit__(self, exc_type, exc, tb):
        print("Exit")
----

Used with `with` statement.

---

== Memory Management

Python uses:
* Reference counting
* Garbage collection

[source,python]
----
import gc
gc.collect()
----

---

== Shallow vs Deep Copy

[source,python]
----
import copy
a = [1, [2,3]]
b = copy.copy(a)
c = copy.deepcopy(a)
----

---

== Multithreading vs Multiprocessing

=== Threading (GIL)

[source,python]
----
import threading

def task():
    print("Working")

t = threading.Thread(target=task)
t.start()
----

Threads share memory but are limited by GIL.

---

=== Multiprocessing

[source,python]
----
from multiprocessing import Process

def task():
    print("Working")

p = Process(target=task)
p.start()
----

Uses multiple CPU cores.

---

== Async Programming (async / await)

[source,python]
----
import asyncio

async def main():
    await asyncio.sleep(1)
    print("Done")

asyncio.run(main())
----

Used for I/O-bound concurrency.

---

== Virtual Environments

[source,bash]
----
python -m venv venv
source venv/bin/activate
----

Isolates dependencies.

---

== Python vs C++ (Quick Comparison)

| Python | C++ |
|-----|-----|
| Interpreted | Compiled |
| Dynamic typing | Static typing |
| Slower | Faster |
| High productivity | High performance |

Python complements C++ well.

---

== Common Interview Questions

* Difference between list and tuple?
* What is GIL?
* What are decorators?
* Generator vs list?
* Shallow vs deep copy?

---

== Best Practices

* Use virtual environments
* Write readable code
* Use standard libraries
* Handle exceptions properly
* Avoid premature optimization

---

== One-Line Takeaway

Python is a powerful, expressive language that prioritizes readability and productivity, while still offering advanced features for scalable and concurrent applications.

