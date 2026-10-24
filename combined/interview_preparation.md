# Interview Preparation

A single, de-duplicated reference merged from:
`cpp_interview_questions_answers_with_code.md`, `scb_cplusplus_interview_questions_answers.md`
and `scb_skills_and_qualifications.md`.

It covers **commonly asked C++ interview questions** — including those asked at Standard Chartered Bank (SCB) for C++ / systems / e-trading roles — with **clear, practical answers and sample code**.

Focus areas:
- C++ fundamentals, OOP, and modern C++ (C++11/14/17)
- Memory management
- STL usage and trade-offs
- Multithreading and concurrency
- Low-latency and system design thinking
- Linux and debugging basics
- Supporting skills: Boost, Python, SQL, communication

## Contents

1. [C++ Basics](#c-basics)
2. [Memory Management](#memory-management)
3. [Object-Oriented Programming](#object-oriented-programming)
4. [Constructors, RAII and Object Lifetime](#constructors-raii-and-object-lifetime)
5. [Templates and Exception Handling](#templates-and-exception-handling)
6. [STL and Containers](#stl-and-containers)
7. [Smart Pointers](#smart-pointers)
8. [Modern C++ (C++11/14/17)](#modern-c-c111417)
9. [Multithreading and Concurrency](#multithreading-and-concurrency)
10. [Low-Latency and System Design](#low-latency-and-system-design)
11. [Linux, Tooling and Debugging](#linux-tooling-and-debugging)
12. [Design Patterns](#design-patterns)
13. [Boost Libraries](#boost-libraries)
14. [Python (Support Skill)](#python-support-skill)
15. [Database and SQL](#database-and-sql)
16. [Communication and Behavioral](#communication-and-behavioral)
17. [Final Interview Advice](#final-interview-advice)

---

## C++ Basics

### What is C++?

C++ is a general-purpose systems programming language providing low-level control with high-level abstractions. It supports:

- Procedural programming
- Object-oriented programming
- Generic programming
- Low-level memory manipulation

Key concepts: RAII, move semantics, zero-cost abstractions. It is widely used in performance-critical systems.

### Difference between C and C++

| C | C++ |
|--|--|
| Procedural | Multi-paradigm |
| No classes | Supports OOP |
| No function overloading | Function overloading |
| Manual memory handling | RAII, smart pointers |

### Difference between pointer and reference

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

## Memory Management

### Stack vs Heap

- **Stack**: automatic memory, fast, limited size
- **Heap**: dynamic memory, flexible, slower, manual or managed

```cpp
int x = 10;          // stack
int* p = new int;    // heap
delete p;
```

Low-latency systems prefer stack allocation when possible.

### What is a memory leak?

Memory allocated but never released.

```cpp
void leak() {
    int* p = new int(5); // not deleted
}
```

Use smart pointers to avoid leaks.

### What is a dangling pointer?

Pointer pointing to freed (deallocated) memory.

```cpp
int* p = new int(10);
delete p;
p = nullptr;
```

Always nullify pointers after deletion.

---

## Object-Oriented Programming

### What is a class and object?

```cpp
class Car {
public:
    void drive() {}
};

Car c; // object
```

### What is encapsulation?

Binding data and methods together.

```cpp
class Bank {
private:
    int balance;
public:
    int getBalance() { return balance; }
};
```

### What is inheritance?

```cpp
class Animal {};
class Dog : public Animal {};
```

### What is polymorphism?

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

### What is a virtual function?

Function resolved at runtime using the vtable.

```cpp
virtual void display();
```

---

## Constructors, RAII and Object Lifetime

### What is a constructor?

Special function called on object creation.

```cpp
class A {
public:
    A() {}
};
```

### What is a destructor?

Called when the object goes out of scope.

```cpp
~A() {}
```

### What is RAII and why is it important?

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

```cpp
#include <fstream>

void writeFile() {
    std::ofstream file("data.txt");
    file << "Hello";
} // file closed automatically
```

RAII is fundamental for writing safe and exception-resilient C++ code.

### Explain Rule of 3 / 5 / 0

If a class manages resources, it must define:

- Rule of 3: destructor, copy constructor, copy assignment
- Rule of 5: + move constructor, move assignment
- Rule of 0: use RAII types so no custom management is needed

Rule of 0 is preferred in modern C++.

### Copy vs Move Semantics

Copy constructor:

```cpp
A(const A& other);
```

Move constructor — transfers ownership:

```cpp
A(A&& other) noexcept;
```

```cpp
std::vector<int> v2 = std::move(v1);
```

Returning a local is moved, not copied:

```cpp
#include <vector>

std::vector<int> create() {
    std::vector<int> v = {1,2,3};
    return v; // moved, not copied
}
```

---

## Templates and Exception Handling

### What are templates?

Templates support generic programming.

```cpp
template<typename T>
T add(T a, T b) {
    return a + b;
}
```

### How does exception handling work?

```cpp
try {
    throw std::runtime_error("error");
}
catch (const std::exception& e) {
    std::cout << e.what();
}
```

---

## STL and Containers

### What is STL?

STL provides generic:

- Containers
- Algorithms
- Iterators

```cpp
#include <vector>
#include <algorithm>

int main() {
    std::vector<int> v = {4,1,3};
    std::sort(v.begin(), v.end());
}
```

Lambda with an algorithm:

```cpp
std::count_if(v.begin(), v.end(),
              [](int x){ return x > 2; });
```

### Difference between vector and list

| vector | list |
|------|------|
| Contiguous memory | Non-contiguous |
| Fast random access | No random access |
| Cache friendly | Poor cache locality |
| Insertion costly in middle | Fast insertion |

Trade-off: choose `vector` for performance, `list` for frequent middle insertions.

### map vs unordered_map

| map | unordered_map |
|----|---------------|
| Ordered | Unordered |
| O(log n) | O(1) average |
| Tree-based | Hash-based |

Trade-off:
- `map` when ordering is required
- `unordered_map` when lookup speed matters

### Iterator invalidation – explain

Some STL operations invalidate iterators.

```cpp
std::vector<int> v = {1,2,3};
auto it = v.begin();
v.push_back(4); // it may be invalid
```

Understanding iterator invalidation avoids subtle bugs.

---

## Smart Pointers

### unique_ptr vs shared_ptr

- `unique_ptr`: exclusive ownership, zero overhead
- `shared_ptr`: reference-counted shared ownership

```cpp
std::unique_ptr<int> u = std::make_unique<int>(5);

std::shared_ptr<int> p1 = std::make_shared<int>(10);
std::shared_ptr<int> p2 = p1;   // shared ownership
```

Prefer `unique_ptr` unless sharing is required.

### What is weak_ptr?

A non-owning reference to a `shared_ptr` that prevents cyclic dependency.

---

## Modern C++ (C++11/14/17)

Modern C++ improves safety and expressiveness.

### What is constexpr?

Compile-time evaluation.

```cpp
constexpr int square(int x) {
    return x * x;
}
```

C++14 allows loops in `constexpr` functions:

```cpp
constexpr int factorial(int n) {
    int r = 1;
    for (int i = 1; i <= n; ++i)
        r *= i;
    return r;
}
```

### Difference between const and constexpr

| const | constexpr |
|-----|-----------|
| Value cannot be modified; may be runtime | Evaluated at compile time |
| No compile-time guarantee | Guaranteed |

```cpp
const int x = runtimeValue();
constexpr int y = 10;
```

`constexpr` enables compile-time computation and better optimization.

### What is a lambda expression?

```cpp
auto f = [](int x){ return x * x; };
```

Generic lambda (C++14):

```cpp
auto add = [](auto a, auto b) {
    return a + b;
};
```

---

## Multithreading and Concurrency

Multithreading allows concurrent execution of multiple threads within a process.

Key goals:
- Improve throughput
- Reduce latency
- Utilize multi-core CPUs

### How do you create a thread?

```cpp
#include <thread>
#include <iostream>

void worker() {
    std::cout << "Worker thread running\n";
}

int main() {
    std::thread t(worker);
    t.join();

    std::thread t2([]{
        // work
    });
    t2.join();
}
```

### What is a race condition?

Occurs when multiple threads access shared data without synchronization.

```cpp
int x = 0;
void f() { x++; } // unsafe
```

Must be protected using a mutex or atomic operations.

### What is a mutex?

Protects shared data.

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

### mutex vs spinlock

- mutex: blocks thread, OS scheduling
- spinlock: busy-waits, no context switch

Trade-off:
- mutex → safer, slower under contention
- spinlock → faster for very short critical sections

### What is a deadlock?

Threads wait indefinitely for each other.

Common causes:
- Circular wait
- Multiple locks in different order

Solution:
- Consistent lock ordering
- Scoped locking

### What is a condition variable?

Used when a thread must wait for a condition to become true. Avoids busy-waiting.

```cpp
std::condition_variable cv;
```

---

## Low-Latency and System Design

### What is low-latency programming?

Focus on predictable execution time.

Techniques:
- Avoid heap allocation
- Avoid locks
- Cache-friendly data

```cpp
int buffer[1024]; // stack allocation, no dynamic allocation in hot path
```

### What is latency vs throughput?

- Latency: time to process a single request
- Throughput: number of requests per second

Low-latency systems prioritize **predictability** over throughput.

### Why avoid heap allocation in hot paths?

- Heap allocation is slow
- Causes latency jitter
- Involves locks internally

Trade-off: reduced flexibility for predictable performance.

### TCP vs UDP

- TCP: reliable, ordered, higher latency
- UDP: fast, unordered, best-effort, no guarantee

Trading systems often use UDP for market data. System-level networking uses POSIX sockets:

```cpp
#include <sys/socket.h>
#include <netinet/in.h>
```

### Distributed Systems Architecture

Distributed systems involve multiple cooperating processes.

Key ideas:
- Stateless services
- Horizontal scaling
- Failure handling

```text
Producer --> Message Queue --> Consumer
```

---

## Linux, Tooling and Debugging

### What tools are commonly used?

- gcc – compiler
- gdb – debugger
- git – version control

```bash
g++ -g main.cpp -o app
gdb ./app
```

### How do you debug a crash?

- Check logs
- Analyze core dump
- Use gdb backtrace

```bash
gdb ./app core
bt
```

### How do you detect memory leaks?

- valgrind
- sanitizers
- code review and RAII usage

---

## Design Patterns

### What is Singleton?

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

## Boost Libraries

Boost extends the C++ standard library.

### Boost.Optional

```cpp
#include <boost/optional.hpp>

boost::optional<int> getValue(bool ok) {
    if (ok) return 42;
    return boost::none;
}
```

### Boost.Asio (Timer)

```cpp
#include <boost/asio.hpp>

int main() {
    boost::asio::io_context io;
    boost::asio::steady_timer t(io, std::chrono::seconds(1));
    t.wait();
}
```

---

## Python (Support Skill)

### Why is Python used with C++?

- Automation
- Testing
- Monitoring
- Tooling and scripting

Python is not used in latency-critical paths.

```python
import os

for file in os.listdir("."):
    print(file)
```

```python
def add(a, b):
    return a + b
```

---

## Database and SQL

Relational databases store structured data with ACID guarantees.

### What is SQL?

Structured Query Language.

```sql
SELECT * FROM users;

SELECT name, salary
FROM employee
WHERE salary > 50000;
```

### What is DDL?

DDL (Data Definition Language) defines the database schema.

```sql
CREATE TABLE orders (
    id INT PRIMARY KEY,
    amount DECIMAL(10,2)
);

ALTER TABLE orders
ADD COLUMN created_at TIMESTAMP;
```

---

## Communication and Behavioral

Engineers must communicate clearly — including with business teams:

- Design decisions and their justification
- Trade-offs
- Risks
- Implementation details

Clear documentation and discussions reduce system failures.

### How do you explain trade-offs?

A good answer explains:

- Options
- Pros and cons
- Reason for final choice

Example:
> “I chose unordered_map for faster lookup, trading ordering and memory usage for speed.”

---

## Final Interview Advice

- Prefer modern C++ features
- Avoid raw pointers
- Understand memory and threading deeply
- Explain **why**, not just **how**

SCB values:
- Clear thinking
- Safe code
- Trade-off awareness
- Communication clarity

They do NOT value:
- Memorized answers
- Overconfidence
- Guesswork

Mastering these areas with **deep understanding** and **clear explanations** prepares you for **system-level, low-latency, and modern C++ roles**.
