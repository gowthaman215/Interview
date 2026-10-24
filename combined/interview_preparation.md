# Interview Preparation

This file combines the following references into one document:

1. [C++ Interview Questions and Answers (With Code Examples)](#part-1) — from `cpp_interview_questions_answers_with_code.md`
2. [Standard Chartered Bank (SCB) – C++ Technical Interview Questions & Answers](#part-2) — from `scb_cplusplus_interview_questions_answers.md`
3. [Technical Skills with Code Examples (C++ / Systems / Python / Database)](#part-3) — from `scb_skills_and_qualifications.md`

---

<a id="part-1"></a>

*Part 1 — source: `cpp_interview_questions_answers_with_code.md`*

## C++ Interview Questions and Answers (With Code Examples)

### Introduction

This document provides a structured collection of **commonly asked C++ interview questions**, along with **technical explanations and sample code**.
It covers fundamentals, OOP, memory management, STL, modern C++, and advanced topics.

---

### C++ Basics

#### What is C++?

C++ is a general-purpose programming language that supports:

- Procedural programming
- Object-oriented programming
- Generic programming
- Low-level memory manipulation

It is widely used in performance-critical systems.

---

#### Difference between C and C++

| C | C++ |
|--|--|
| Procedural | Multi-paradigm |
| No classes | Supports OOP |
| No function overloading | Function overloading |
| Manual memory handling | RAII, smart pointers |

---

### Memory Management

#### What is stack and heap?

- **Stack**: Automatic memory, fast, limited size
- **Heap**: Dynamic memory, flexible, slower

```cpp
int x = 10;          // stack
int* p = new int;   // heap
delete p;
```

---

#### What is a memory leak?

Memory allocated but not released.

```cpp
void leak() {
    int* p = new int(5); // not deleted
}
```

Avoid using smart pointers.

---

#### What is dangling pointer?

Pointer pointing to deallocated memory.

```cpp
int* p = new int(10);
delete p;
p = nullptr;
```

---

### Pointers and References

#### Difference between pointer and reference

| Pointer | Reference |
|-------|-----------|
| Can be null | Cannot be null |
| Can change target | Cannot rebind |
| Uses * | Acts like alias |

```cpp
int x = 10;
int* p = &x;
int& r = x;
```

---

### Object Oriented Programming

#### What is a class and object?

```cpp
class Car {
public:
    void drive() {}
};

Car c; // object
```

---

#### What is encapsulation?

Binding data and methods together.

```cpp
class Bank {
private:
    int balance;
public:
    int getBalance() { return balance; }
};
```

---

#### What is inheritance?

```cpp
class Animal {};
class Dog : public Animal {};
```

---

#### What is polymorphism?

Ability to take many forms.

```cpp
class Base {
public:
    virtual void show() {}
};

class Derived : public Base {
public:
    void show() override {}
};
```

---

#### What is virtual function?

Function resolved at runtime using vtable.

```cpp
virtual void display();
```

---

### Constructors and Destructors

#### What is a constructor?

Special function called on object creation.

```cpp
class A {
public:
    A() {}
};
```

---

#### What is destructor?

Called when object goes out of scope.

```cpp
~A() {}
```

---

### Copy vs Move Semantics

#### What is copy constructor?

```cpp
A(const A& other);
```

---

#### What is move constructor?

Transfers ownership.

```cpp
A(A&& other) noexcept;
```

```cpp
std::vector<int> v2 = std::move(v1);
```

---

### Templates

#### What are templates?

Templates support generic programming.

```cpp
template<typename T>
T add(T a, T b) {
    return a + b;
}
```

---

### Exception Handling

#### How does exception handling work?

```cpp
try {
    throw std::runtime_error("error");
}
catch (const std::exception& e) {
    std::cout << e.what();
}
```

---

### Standard Template Library (STL)

#### What is STL?

STL consists of:

- Containers
- Algorithms
- Iterators

---

#### Example: vector and algorithm

```cpp
std::vector<int> v = {3,1,2};
std::sort(v.begin(), v.end());
```

---

#### Difference between map and unordered_map

| map | unordered_map |
|----|---------------|
| Ordered | Unordered |
| O(log n) | O(1) avg |
| Tree based | Hash based |

---

### Smart Pointers

#### What is unique_ptr?

```cpp
std::unique_ptr<int> p = std::make_unique<int>(5);
```

---

#### What is shared_ptr?

```cpp
std::shared_ptr<int> p1 = std::make_shared<int>(10);
std::shared_ptr<int> p2 = p1;
```

---

#### What is weak_ptr?

Prevents cyclic dependency.

---

### Multithreading

#### How do you create a thread?

```cpp
#include <thread>

std::thread t([]{
    // work
});
t.join();
```

---

#### What is mutex?

Protects shared data.

```cpp
std::mutex m;
std::lock_guard<std::mutex> lock(m);
```

---

### Modern C++ (C++11/14/17)

#### What is constexpr?

Compile-time evaluation.

```cpp
constexpr int square(int x) {
    return x * x;
}
```

---

#### Difference between const and constexpr

| const | constexpr |
|-----|-----------|
| May be runtime | Compile-time |
| No guarantee | Guaranteed |

---

#### What is lambda expression?

```cpp
auto f = [](int x){ return x * x; };
```

---

### Database and SQL

#### What is SQL?

Structured Query Language.

```sql
SELECT * FROM users;
```

---

#### What is DDL?

Defines schema.

```sql
CREATE TABLE users(id INT);
```

---

### Linux and Tooling

#### What tools are commonly used?

- gcc – compiler
- gdb – debugger
- git – version control

```bash
g++ main.cpp
gdb ./a.out
```

---

### System and Low Latency Programming

#### What is low latency programming?

Focus on predictable execution.

Techniques:
- Avoid heap allocation
- Avoid locks
- Cache-friendly data

---

### Design Patterns

#### What is Singleton?

```cpp
class Singleton {
public:
    static Singleton& instance() {
        static Singleton s;
        return s;
    }
};
```

---

### Communication Skills (Interview Context)

Engineers must:

- Explain trade-offs
- Justify design
- Communicate clearly with business teams

---

### Final Interview Tips

- Prefer modern C++ features
- Avoid raw pointers
- Understand memory and threading deeply
- Explain **why**, not just **how**

---

### Conclusion

This document covers **core to advanced C++ interview topics** with explanations and code.
Mastering these areas prepares you for **system-level, low-latency, and modern C++ roles**.

---

<a id="part-2"></a>

*Part 2 — source: `scb_cplusplus_interview_questions_answers.md`*

## Standard Chartered Bank (SCB) – C++ Technical Interview Questions & Answers

### Introduction

This document contains a curated list of **commonly asked technical interview questions at Standard Chartered Bank (SCB)** for C++ / systems / e-trading roles, along with **clear and practical answers**.

Focus areas include:
- Modern C++ (C++14/17)
- Multithreading and concurrency
- Memory management
- STL usage and trade-offs
- Low-latency and system design thinking
- Linux and debugging basics

---

### Core C++ Questions

#### What is RAII and why is it important?

RAII (Resource Acquisition Is Initialization) binds the lifetime of a resource to the lifetime of an object.

- Resources are acquired in the constructor
- Resources are released in the destructor
- Guarantees no leaks, even during exceptions

```cpp
class File {
    std::ofstream f;
public:
    File() : f("data.txt") {}
}; // file automatically closed
```

RAII is fundamental for writing safe and exception-resilient C++ code.

---

#### Difference between const and constexpr

- `const` means the value cannot be modified
- `constexpr` means the value can be evaluated at compile time

```cpp
const int x = runtimeValue();
constexpr int y = 10;
```

`constexpr` enables compile-time computation and better optimization.

---

#### Explain Rule of 3 / 5 / 0

If a class manages resources, it must define:

- Rule of 3: destructor, copy constructor, copy assignment
- Rule of 5: + move constructor, move assignment
- Rule of 0: use RAII types so no custom management is needed

Rule of 0 is preferred in modern C++.

---

### STL and Containers

#### Difference between vector and list

| vector | list |
|------|------|
| Contiguous memory | Non-contiguous |
| Fast random access | No random access |
| Cache friendly | Poor cache locality |
| Insertion costly in middle | Fast insertion |

Trade-off: choose `vector` for performance, `list` for frequent middle insertions.

---

#### map vs unordered_map

| map | unordered_map |
|----|---------------|
| Ordered | Unordered |
| O(log n) | O(1) average |
| Tree-based | Hash-based |

Trade-off:
- `map` when ordering is required
- `unordered_map` when lookup speed matters

---

#### Iterator invalidation – explain

Some STL operations invalidate iterators.

```cpp
std::vector<int> v = {1,2,3};
auto it = v.begin();
v.push_back(4); // it may be invalid
```

Understanding iterator invalidation avoids subtle bugs.

---

### Memory Management

#### Stack vs Heap

- Stack: fast, automatic, limited size
- Heap: flexible, slower, manual or managed

```cpp
int x = 10;        // stack
int* p = new int; // heap
delete p;
```

Low-latency systems prefer stack allocation when possible.

---

#### What is a memory leak?

Memory allocated but never released.

```cpp
void leak() {
    int* p = new int(5);
}
```

Use smart pointers to avoid leaks.

---

#### What is a dangling pointer?

Pointer pointing to freed memory.

```cpp
int* p = new int(10);
delete p;
p = nullptr;
```

Always nullify pointers after deletion.

---

### Smart Pointers

#### unique_ptr vs shared_ptr

- `unique_ptr`: exclusive ownership, zero overhead
- `shared_ptr`: reference-counted shared ownership

```cpp
auto u = std::make_unique<int>(5);
auto s = std::make_shared<int>(10);
```

Prefer `unique_ptr` unless sharing is required.

---

### Multithreading & Concurrency

#### What is a race condition?

Occurs when multiple threads access shared data without synchronization.

```cpp
int x = 0;
void f() { x++; } // unsafe
```

Must be protected using mutex or atomic operations.

---

#### mutex vs spinlock

- mutex: blocks thread, OS scheduling
- spinlock: busy-waits, no context switch

Trade-off:
- mutex → safer, slower under contention
- spinlock → faster for very short critical sections

---

#### What is a deadlock?

Threads wait indefinitely for each other.

Common causes:
- Circular wait
- Multiple locks in different order

Solution:
- Consistent lock ordering
- Scoped locking

---

#### What is a condition variable?

Used when a thread must wait for a condition to become true.

```cpp
std::condition_variable cv;
```

Avoids busy-waiting.

---

### Low-Latency & System Design

#### What is latency vs throughput?

- Latency: time to process a single request
- Throughput: number of requests per second

Low-latency systems prioritize **predictability** over throughput.

---

#### Why avoid heap allocation in hot paths?

- Heap allocation is slow
- Causes latency jitter
- Involves locks internally

Trade-off: reduced flexibility for predictable performance.

---

#### TCP vs UDP

- TCP: reliable, ordered, higher latency
- UDP: fast, unordered, no guarantee

Trading systems often use UDP for market data.

---

### Linux & Debugging

#### How do you debug a crash?

- Check logs
- Analyze core dump
- Use gdb backtrace

```bash
gdb ./app core
bt
```

---

#### How do you detect memory leaks?

- valgrind
- sanitizers
- code review and RAII usage

---

### Python & SQL (Support Skills)

#### Why Python is used with C++?

- Automation
- Testing
- Monitoring
- Tooling

Python is not used in latency-critical paths.

---

#### What is DDL?

DDL defines database schema.

```sql
CREATE TABLE trades(id INT);
```

---

### Behavioral & Communication

#### How do you explain trade-offs?

A good answer explains:

- Options
- Pros and cons
- Reason for final choice

Example:
> “I chose unordered_map for faster lookup, trading ordering and memory usage for speed.”

---

### Final Interview Advice

SCB values:
- Clear thinking
- Safe code
- Trade-off awareness
- Communication clarity

They do NOT value:
- Memorized answers
- Overconfidence
- Guesswork

---

### Conclusion

Preparing these questions with **deep understanding** and **clear explanations** aligns strongly with SCB’s interview expectations for C++ and systems roles.

---

<a id="part-3"></a>

*Part 3 — source: `scb_skills_and_qualifications.md`*

## Technical Skills with Code Examples (C++ / Systems / Python / Database)

### Multithreading

Multithreading allows concurrent execution of multiple threads within a process.

Key goals:
- Improve throughput
- Reduce latency
- Utilize multi-core CPUs

#### Example: Basic Thread Creation

```cpp
#include <thread>
#include <iostream>

void worker() {
    std::cout << "Worker thread running\n";
}

int main() {
    std::thread t(worker);
    t.join();
}
```

#### Example: Synchronization Using Mutex

```cpp
#include <mutex>
#include <thread>

std::mutex mtx;
int counter = 0;

void increment() {
    std::lock_guard<std::mutex> lock(mtx);
    ++counter;
}

int main() {
    std::thread t1(increment);
    std::thread t2(increment);
    t1.join();
    t2.join();
}
```

---

### C++

C++ is a systems programming language providing low-level control with high-level abstractions.

Key concepts:
- RAII
- Move semantics
- Zero-cost abstractions

#### Example: RAII for Resource Management

```cpp
#include <fstream>

void writeFile() {
    std::ofstream file("data.txt");
    file << "Hello";
} // file closed automatically
```

#### Example: Move Semantics

```cpp
#include <vector>

std::vector<int> create() {
    std::vector<int> v = {1,2,3};
    return v; // moved, not copied
}
```

---

### Standard Template Library (STL)

STL provides generic containers, algorithms, and iterators.

#### Example: Container + Algorithm

```cpp
#include <vector>
#include <algorithm>

int main() {
    std::vector<int> v = {4,1,3};
    std::sort(v.begin(), v.end());
}
```

#### Example: Lambda with Algorithm

```cpp
std::count_if(v.begin(), v.end(),
              [](int x){ return x > 2; });
```

---

### Boost Libraries

Boost extends the C++ standard library.

#### Example: Boost.Optional

```cpp
#include <boost/optional.hpp>

boost::optional<int> getValue(bool ok) {
    if (ok) return 42;
    return boost::none;
}
```

#### Example: Boost.Asio (Timer)

```cpp
#include <boost/asio.hpp>

int main() {
    boost::asio::io_context io;
    boost::asio::steady_timer t(io, std::chrono::seconds(1));
    t.wait();
}
```

---

### Python

Python is used for tooling, automation, and scripting.

#### Example: Simple Automation Script

```python
import os

for file in os.listdir("."):
    print(file)
```

#### Example: Simple Function

```python
def add(a, b):
    return a + b
```

---

### Database (SQL)

Relational databases store structured data with ACID guarantees.

#### Example: Basic SQL Query

```sql
SELECT name, salary
FROM employee
WHERE salary > 50000;
```

#### Example: Simple DDL Operation

```sql
CREATE TABLE orders (
    id INT PRIMARY KEY,
    amount DECIMAL(10,2)
);
```

---

### Modern C++14 / C++17 Features

Modern C++ improves safety and expressiveness.

#### Example: Generic Lambda (C++14)

```cpp
auto add = [](auto a, auto b) {
    return a + b;
};
```

#### Example: constexpr Function (C++14)

```cpp
constexpr int factorial(int n) {
    int r = 1;
    for (int i = 1; i <= n; ++i)
        r *= i;
    return r;
}
```

---

### System, Network, and Low-Latency Programming

Low-latency systems prioritize predictable execution time.

#### Example: Avoid Dynamic Allocation in Hot Path

```cpp
int buffer[1024]; // stack allocation
```

#### Example: TCP Socket (POSIX)

```cpp
#include <sys/socket.h>
#include <netinet/in.h>
```

(Used for system-level networking.)

---

### Linux Development Environment

Linux provides powerful development tools.

#### Example: Compile and Debug

```bash
g++ -g main.cpp -o app
gdb ./app
```

---

### Distributed Systems Architecture

Distributed systems involve multiple cooperating processes.

Key ideas:
- Stateless services
- Horizontal scaling
- Failure handling

#### Example: Simple Producer-Consumer Concept

```text
Producer --> Message Queue --> Consumer
```

---

### Messaging and Communication Protocols

Protocols define how data is transmitted.

#### Example: TCP vs UDP Concept

```text
TCP: Reliable, ordered
UDP: Fast, best-effort
```

---

### SQL and DDL Operations

DDL defines schema structure.

#### Example: Alter Table

```sql
ALTER TABLE orders
ADD COLUMN created_at TIMESTAMP;
```

---

### Communication Skills (Technical Context)

Engineers must communicate:

- Design decisions
- Trade-offs
- Risks
- Implementation details

Clear documentation and discussions reduce system failures.

---

### Final Summary

This document demonstrates:

- Practical multithreading in C++
- Modern C++14/17 usage
- STL and Boost proficiency
- Python scripting support
- Database and SQL fundamentals
- Systems and low-latency awareness

These skills are essential for building **high-performance, scalable, and reliable systems**.
