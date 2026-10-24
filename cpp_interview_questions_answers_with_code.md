# C++ Interview Questions and Answers (With Code Examples)

## Introduction

This document provides a structured collection of **commonly asked C++ interview questions**, along with **technical explanations and sample code**.
It covers fundamentals, OOP, memory management, STL, modern C++, and advanced topics.

---

## C++ Basics

### What is C++?

C++ is a general-purpose programming language that supports:

- Procedural programming
- Object-oriented programming
- Generic programming
- Low-level memory manipulation

It is widely used in performance-critical systems.

---

### Difference between C and C++

| C | C++ |
|--|--|
| Procedural | Multi-paradigm |
| No classes | Supports OOP |
| No function overloading | Function overloading |
| Manual memory handling | RAII, smart pointers |

---

## Memory Management

### What is stack and heap?

- **Stack**: Automatic memory, fast, limited size
- **Heap**: Dynamic memory, flexible, slower

```cpp
int x = 10;          // stack
int* p = new int;   // heap
delete p;
```

---

### What is a memory leak?

Memory allocated but not released.

```cpp
void leak() {
    int* p = new int(5); // not deleted
}
```

Avoid using smart pointers.

---

### What is dangling pointer?

Pointer pointing to deallocated memory.

```cpp
int* p = new int(10);
delete p;
p = nullptr;
```

---

## Pointers and References

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

## Object Oriented Programming

### What is a class and object?

```cpp
class Car {
public:
    void drive() {}
};

Car c; // object
```

---

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

---

### What is inheritance?

```cpp
class Animal {};
class Dog : public Animal {};
```

---

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

---

### What is virtual function?

Function resolved at runtime using vtable.

```cpp
virtual void display();
```

---

## Constructors and Destructors

### What is a constructor?

Special function called on object creation.

```cpp
class A {
public:
    A() {}
};
```

---

### What is destructor?

Called when object goes out of scope.

```cpp
~A() {}
```

---

## Copy vs Move Semantics

### What is copy constructor?

```cpp
A(const A& other);
```

---

### What is move constructor?

Transfers ownership.

```cpp
A(A&& other) noexcept;
```

```cpp
std::vector<int> v2 = std::move(v1);
```

---

## Templates

### What are templates?

Templates support generic programming.

```cpp
template<typename T>
T add(T a, T b) {
    return a + b;
}
```

---

## Exception Handling

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

## Standard Template Library (STL)

### What is STL?

STL consists of:

- Containers
- Algorithms
- Iterators

---

### Example: vector and algorithm

```cpp
std::vector<int> v = {3,1,2};
std::sort(v.begin(), v.end());
```

---

### Difference between map and unordered_map

| map | unordered_map |
|----|---------------|
| Ordered | Unordered |
| O(log n) | O(1) avg |
| Tree based | Hash based |

---

## Smart Pointers

### What is unique_ptr?

```cpp
std::unique_ptr<int> p = std::make_unique<int>(5);
```

---

### What is shared_ptr?

```cpp
std::shared_ptr<int> p1 = std::make_shared<int>(10);
std::shared_ptr<int> p2 = p1;
```

---

### What is weak_ptr?

Prevents cyclic dependency.

---

## Multithreading

### How do you create a thread?

```cpp
#include <thread>

std::thread t([]{
    // work
});
t.join();
```

---

### What is mutex?

Protects shared data.

```cpp
std::mutex m;
std::lock_guard<std::mutex> lock(m);
```

---

## Modern C++ (C++11/14/17)

### What is constexpr?

Compile-time evaluation.

```cpp
constexpr int square(int x) {
    return x * x;
}
```

---

### Difference between const and constexpr

| const | constexpr |
|-----|-----------|
| May be runtime | Compile-time |
| No guarantee | Guaranteed |

---

### What is lambda expression?

```cpp
auto f = [](int x){ return x * x; };
```

---

## Database and SQL

### What is SQL?

Structured Query Language.

```sql
SELECT * FROM users;
```

---

### What is DDL?

Defines schema.

```sql
CREATE TABLE users(id INT);
```

---

## Linux and Tooling

### What tools are commonly used?

- gcc – compiler
- gdb – debugger
- git – version control

```bash
g++ main.cpp
gdb ./a.out
```

---

## System and Low Latency Programming

### What is low latency programming?

Focus on predictable execution.

Techniques:
- Avoid heap allocation
- Avoid locks
- Cache-friendly data

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

## Communication Skills (Interview Context)

Engineers must:

- Explain trade-offs
- Justify design
- Communicate clearly with business teams

---

## Final Interview Tips

- Prefer modern C++ features
- Avoid raw pointers
- Understand memory and threading deeply
- Explain **why**, not just **how**

---

## Conclusion

This document covers **core to advanced C++ interview topics** with explanations and code.
Mastering these areas prepares you for **system-level, low-latency, and modern C++ roles**.
