# Scripting: Python and Bash

This file combines the following references into one document:

1. [Python Programming — Complete Reference (Basic, Core, Advanced)](#part-1) — from `python_complete_reference_basic_core_advanced.md`
2. [Bash Shell Scripting — Complete Reference (Basic, Core, Advanced)](#part-2) — from `bash_shell_scripting_complete_reference.md`

---

<a id="part-1"></a>

*Part 1 — source: `python_complete_reference_basic_core_advanced.md`*

## Python Programming — Complete Reference (Basic, Core, Advanced)

### Introduction

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

### BASIC PYTHON CONCEPTS

### Variables and Data Types

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

### Input and Output

```python
name = input("Enter name: ")
print("Hello", name)
```

---

### Operators

#### Arithmetic Operators

```python
a = 10 + 5
b = 10 / 3
c = 10 // 3
d = 10 % 3
```

---

#### Comparison Operators

```python
x > y
x == y
x != y
```

---

#### Logical Operators

```python
if x > 0 and y > 0:
    print("Positive")
```

---

### Control Flow

#### if / elif / else

```python
if x > 0:
    print("Positive")
elif x < 0:
    print("Negative")
else:
    print("Zero")
```

---

#### for Loop

```python
for i in range(5):
    print(i)
```

---

#### while Loop

```python
count = 0
while count < 5:
    count += 1
```

---

### Functions

```python
def add(a, b):
    return a + b
```

Key points:
- Functions are first-class objects
- Can be passed as arguments

---

### CORE PYTHON CONCEPTS

### Data Structures

#### List

```python
nums = [1, 2, 3]
nums.append(4)
```

---

#### Tuple

```python
point = (10, 20)
```

Immutable sequence.

---

#### Dictionary

```python
user = {"name": "Alice", "age": 30}
print(user["name"])
```

---

#### Set

```python
s = {1, 2, 3}
s.add(4)
```

---

### String Handling

```python
text = "hello python"
print(text.upper())
print(text.split())
```

---

### List Comprehensions

```python
squares = [x*x for x in range(5)]
```

Concise and efficient.

---

### Exception Handling

```python
try:
    x = int("abc")
except ValueError:
    print("Conversion failed")
finally:
    print("Done")
```

---

### File Handling

```python
with open("data.txt", "w") as f:
    f.write("Hello")
```

`with` ensures automatic cleanup.

---

### Modules and Packages

```python
import math
print(math.sqrt(16))
```

Custom module:
```python
import mymodule
```

---

### Object-Oriented Programming (OOP)

### Classes and Objects

```python
class Car:
    def __init__(self, brand):
        self.brand = brand

    def drive(self):
        print("Driving", self.brand)
```

---

### Inheritance

```python
class ElectricCar(Car):
    pass
```

---

### Encapsulation

```python
class Account:
    def __init__(self):
        self.__balance = 0
```

Double underscore makes attribute private (name mangling).

---

### Polymorphism

```python
def start(car):
    car.drive()
```

Duck typing:
> “If it quacks like a duck, it’s a duck.”

---

### ADVANCED PYTHON CONCEPTS

### Iterators and Generators

#### Iterator

```python
nums = [1,2,3]
it = iter(nums)
print(next(it))
```

---

#### Generator

```python
def count_up(n):
    for i in range(n):
        yield i
```

Generators are memory efficient.

---

### Decorators

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

### Lambda Functions

```python
add = lambda a, b: a + b
```

Used for short, inline functions.

---

### *args and **kwargs

```python
def func(*args, **kwargs):
    print(args)
    print(kwargs)
```

Used for flexible APIs.

---

### Context Managers

```python
class MyContext:
    def __enter__(self):
        print("Enter")
    def __exit__(self, exc_type, exc, tb):
        print("Exit")
```

Used with `with` statement.

---

### Memory Management

Python uses:
- Reference counting
- Garbage collection

```python
import gc
gc.collect()
```

---

### Shallow vs Deep Copy

```python
import copy
a = [1, [2,3]]
b = copy.copy(a)
c = copy.deepcopy(a)
```

---

### Multithreading vs Multiprocessing

#### Threading (GIL)

```python
import threading

def task():
    print("Working")

t = threading.Thread(target=task)
t.start()
```

Threads share memory but are limited by GIL.

---

#### Multiprocessing

```python
from multiprocessing import Process

def task():
    print("Working")

p = Process(target=task)
p.start()
```

Uses multiple CPU cores.

---

### Async Programming (async / await)

```python
import asyncio

async def main():
    await asyncio.sleep(1)
    print("Done")

asyncio.run(main())
```

Used for I/O-bound concurrency.

---

### Virtual Environments

```bash
python -m venv venv
source venv/bin/activate
```

Isolates dependencies.

---

### Python vs C++ (Quick Comparison)

| Python | C++ |
|-----|-----|
| Interpreted | Compiled |
| Dynamic typing | Static typing |
| Slower | Faster |
| High productivity | High performance |

Python complements C++ well.

---

### Common Interview Questions

- Difference between list and tuple?
- What is GIL?
- What are decorators?
- Generator vs list?
- Shallow vs deep copy?

---

### Best Practices

- Use virtual environments
- Write readable code
- Use standard libraries
- Handle exceptions properly
- Avoid premature optimization

---

### One-Line Takeaway

Python is a powerful, expressive language that prioritizes readability and productivity, while still offering advanced features for scalable and concurrent applications.

---

<a id="part-2"></a>

*Part 2 — source: `bash_shell_scripting_complete_reference.md`*

## Bash Shell Scripting — Complete Reference (Basic, Core, Advanced)

### Introduction

Bash (Bourne Again SHell) is a Unix shell and command language.
It is widely used for:

- System automation
- Deployment scripts
- Monitoring and maintenance
- DevOps pipelines
- Daily Linux operations

Bash scripts execute commands sequentially in a shell environment.

---

### BASIC BASH CONCEPTS

### What Is a Shell?

A shell:
- Interprets user commands
- Interacts with the OS kernel
- Executes programs

Common shells:
- bash
- sh
- zsh
- fish

---

### First Bash Script

```bash
#!/bin/bash
echo "Hello, Bash"
```

Make executable:
```bash
chmod +x script.sh
./script.sh
```

---

### Variables

```bash
name="Linux"
echo $name
```

Rules:
- No spaces around `=`
- Access using `$`

---

### Command Substitution

```bash
date_now=$(date)
echo $date_now
```

---

### User Input

```bash
read -p "Enter name: " name
echo "Hello $name"
```

---

### Positional Parameters

```bash
echo $0   # script name
echo $1   # first argument
echo $@   # all arguments
```

---

### Exit Status

```bash
ls /tmp
echo $?   # 0 = success
```

---

### CORE BASH CONCEPTS

### Conditional Statements

#### if / else

```bash
if [ $x -gt 10 ]; then
    echo "Greater"
else
    echo "Smaller"
fi
```

---

### File Tests

```bash
if [ -f file.txt ]; then
    echo "File exists"
fi
```

Common tests:
- `-f` file
- `-d` directory
- `-e` exists
- `-x` executable

---

### Case Statement

```bash
case $1 in
    start) echo "Starting";;
    stop)  echo "Stopping";;
    *)     echo "Unknown";;
esac
```

---

### Loops

#### for Loop

```bash
for i in 1 2 3; do
    echo $i
done
```

---

#### while Loop

```bash
count=0
while [ $count -lt 5 ]; do
    ((count++))
done
```

---

### Functions

```bash
greet() {
    echo "Hello $1"
}
greet "World"
```

---

### Arrays

```bash
arr=(one two three)
echo ${arr[0]}
echo ${arr[@]}
```

---

### String Operations

```bash
str="hello world"
echo ${#str}
echo ${str^^}
```

---

### Input / Output Redirection

```bash
ls > out.txt
ls >> out.txt
ls 2> err.txt
```

---

### Pipes

```bash
ps aux | grep root
```

Pipes connect command outputs to inputs.

---

### ADVANCED BASH CONCEPTS

### Quoting Rules

#### Double Quotes

```bash
echo "$name"
```

Expands variables.

---

#### Single Quotes

```bash
echo '$name'
```

Literal output.

---

### Subshells

```bash
(cd /tmp && ls)
```

Runs in a child shell.

---

### Here Documents

```bash
cat <<EOF
Line 1
Line 2
EOF
```

---

### Process Management

#### Background Jobs

```bash
sleep 10 &
jobs
```

---

#### wait

```bash
wait
```

Waits for background processes.

---

### Signal Handling

```bash
trap "echo Interrupted" SIGINT
```

Handles Ctrl+C.

---

### Environment Variables

```bash
export PATH=$PATH:/opt/bin
```

Available to child processes.

---

### Debugging Bash Scripts

#### Enable Debug Mode

```bash
set -x
```

---

#### Exit on Error

```bash
set -e
```

---

#### Treat Unset Variables as Error

```bash
set -u
```

---

### Cron Jobs (Automation)

```bash
crontab -e
```

Example:
```bash
0 2 * * * /home/user/backup.sh
```

---

### Bash vs Shell (`sh`)

| bash | sh |
|----|----|
| Advanced features | Minimal |
| Arrays | No |
| Better scripting | Portable |

---

### Common Utilities Used in Bash

- grep
- awk
- sed
- cut
- sort
- xargs

---

### Example: Real Automation Script

```bash
#!/bin/bash

LOG="/var/log/app.log"

if [ ! -f "$LOG" ]; then
    echo "Log not found"
    exit 1
fi

grep ERROR "$LOG" | tail -n 10
```

---

### Performance and Best Practices

- Quote variables
- Use functions
- Avoid unnecessary subshells
- Prefer `[[ ]]` over `[ ]`
- Check exit codes
- Keep scripts readable

---

### Common Interview Questions

- Difference between `$@` and `$*`
- `&&` vs `||`
- How to debug bash script?
- What is a subshell?
- Difference between `source` and `./script.sh`

---

### One-Line Takeaway

Bash scripting is a powerful tool for automating system tasks, orchestrating programs, and managing Linux environments efficiently.
