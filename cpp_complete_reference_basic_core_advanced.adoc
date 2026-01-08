= C++ Programming — Complete Reference (Basic, Core, Advanced)
:toc:
:toclevels: 4
:sectnums:
:source-highlighter: coderay

== Introduction

C++ is a high-performance, general-purpose programming language supporting:
* Procedural programming
* Object-Oriented Programming
* Generic programming
* Low-level system access

C++ is widely used in:
* Trading systems
* Databases
* Operating systems
* Game engines
* High-performance servers

---

== BASIC C++ CONCEPTS

== Program Structure

[source,cpp]
----
#include <iostream>

int main() {
    std::cout << "Hello C++";
    return 0;
}
----

Execution starts from `main()`.

---

== Variables and Data Types

[source,cpp]
----
int a = 10;
double b = 3.14;
char c = 'x';
bool flag = true;
----

C++ is statically typed.

---

== Constants

[source,cpp]
----
const int x = 10;
constexpr int y = 20;
----

* `const` → read-only
* `constexpr` → compile-time constant

---

== Operators

=== Arithmetic

[source,cpp]
----
int a = 10 + 5;
int b = 10 / 3;
int c = 10 % 3;
----

---

=== Comparison and Logical

[source,cpp]
----
if (a > b && c != 0) {}
----

---

== Control Flow

=== if / else

[source,cpp]
----
if (x > 0) {
    std::cout << "Positive";
} else {
    std::cout << "Negative";
}
----

---

=== for Loop

[source,cpp]
----
for (int i = 0; i < 5; ++i) {
    std::cout << i;
}
----

---

=== while Loop

[source,cpp]
----
while (x > 0) {
    x--;
}
----

---

== Functions

[source,cpp]
----
int add(int a, int b) {
    return a + b;
}
----

---

== CORE C++ CONCEPTS

== Pointers and References

[source,cpp]
----
int x = 10;
int* p = &x;
int& r = x;
----

Pointers store addresses; references are aliases.

---

== Memory Management

=== Stack vs Heap

[source,cpp]
----
int a = 10;          // stack
int* p = new int(5); // heap
delete p;
----

---

== RAII (Resource Acquisition Is Initialization)

[source,cpp]
----
#include <fstream>

void write() {
    std::ofstream f("data.txt");
    f << "Hello";
} // file closed automatically
----

RAII ensures deterministic cleanup.

---

== Object-Oriented Programming

== Classes and Objects

[source,cpp]
----
class Car {
public:
    void drive() {
        std::cout << "Driving";
    }
};
----

---

== Constructors and Destructors

[source,cpp]
----
class A {
public:
    A() { }
    ~A() { }
};
----

---

== Encapsulation

[source,cpp]
----
class Account {
private:
    int balance;
public:
    int getBalance() const { return balance; }
};
----

---

== Inheritance

[source,cpp]
----
class Base {};
class Derived : public Base {};
----

---

== Polymorphism

[source,cpp]
----
class Base {
public:
    virtual void show() {}
};

class Derived : public Base {
public:
    void show() override {}
};
----

---

== Rule of 0 / 3 / 5

* Rule of 0: Use RAII, no custom destructor
* Rule of 3: Destructor, copy ctor, copy assign
* Rule of 5: + move ctor, move assign

---

== Templates

[source,cpp]
----
template<typename T>
T add(T a, T b) {
    return a + b;
}
----

---

== Exception Handling

[source,cpp]
----
try {
    throw std::runtime_error("error");
} catch (const std::exception& e) {
    std::cout << e.what();
}
----

---

== STL (Standard Template Library)

== Containers

[source,cpp]
----
std::vector<int> v = {1,2,3};
std::map<int,int> m;
----

---

== Algorithms

[source,cpp]
----
std::sort(v.begin(), v.end());
----

---

== Iterators

[source,cpp]
----
for (auto it = v.begin(); it != v.end(); ++it) {}
----

---

== ADVANCED C++ CONCEPTS

== Move Semantics

[source,cpp]
----
std::vector<int> v2 = std::move(v1);
----

Moves resources instead of copying.

---

== Smart Pointers

[source,cpp]
----
#include <memory>

std::unique_ptr<int> u = std::make_unique<int>(10);
std::shared_ptr<int> s = std::make_shared<int>(20);
----

---

== Lambda Expressions

[source,cpp]
----
auto f = [](int x) { return x * x; };
----

---

== Multithreading

[source,cpp]
----
#include <thread>

void task() {}

std::thread t(task);
t.join();
----

---

== Mutex and Synchronization

[source,cpp]
----
#include <mutex>

std::mutex m;
std::lock_guard<std::mutex> lock(m);
----

---

== Atomic Operations

[source,cpp]
----
#include <atomic>

std::atomic<int> counter{0};
counter++;
----

---

== Memory Model and Visibility

Synchronization ensures:
* Happens-before relationships
* Visibility across threads

---

== Low-Latency Programming Concepts

* Avoid heap allocations in hot paths
* Avoid locks where possible
* Prefer cache-friendly data
* Use fixed-size containers

---

== Design Patterns (Common)

=== Singleton

[source,cpp]
----
class Singleton {
public:
    static Singleton& instance() {
        static Singleton s;
        return s;
    }
};
----

---

=== Factory (Concept)

[source,text]
----
Create objects without exposing creation logic
----

---

== C++ vs Python (Quick View)

| C++ | Python |
|----|-------|
| Compiled | Interpreted |
| Fast | Slower |
| Manual memory control | GC |
| Low-level | High-level |

---

== Common Interview Questions

* Difference between `const` and `constexpr`?
* Why virtual destructor?
* vector vs list?
* map vs unordered_map?
* What is RAII?
* What happens if std::thread not joined?

---

== Best Practices

* Prefer modern C++
* Use RAII everywhere
* Avoid raw pointers
* Prefer STL algorithms
* Measure before optimizing
* Write readable code

---

== One-Line Takeaway

C++ provides fine-grained control over performance and resources, making it ideal for building safe, efficient, and scalable systems when used with modern best practices.

