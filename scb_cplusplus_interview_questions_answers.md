= Standard Chartered Bank (SCB) – C++ Technical Interview Questions & Answers
:toc:
:toclevels: 3
:sectnums:
:source-highlighter: coderay

== Introduction

This document contains a curated list of **commonly asked technical interview questions at Standard Chartered Bank (SCB)** for C++ / systems / e-trading roles, along with **clear and practical answers**.

Focus areas include:
* Modern C++ (C++14/17)
* Multithreading and concurrency
* Memory management
* STL usage and trade-offs
* Low-latency and system design thinking
* Linux and debugging basics

---

== Core C++ Questions

=== What is RAII and why is it important?

RAII (Resource Acquisition Is Initialization) binds the lifetime of a resource to the lifetime of an object.

* Resources are acquired in the constructor
* Resources are released in the destructor
* Guarantees no leaks, even during exceptions

[source,cpp]
----
class File {
    std::ofstream f;
public:
    File() : f("data.txt") {}
}; // file automatically closed
----

RAII is fundamental for writing safe and exception-resilient C++ code.

---

=== Difference between const and constexpr

* `const` means the value cannot be modified
* `constexpr` means the value can be evaluated at compile time

[source,cpp]
----
const int x = runtimeValue();
constexpr int y = 10;
----

`constexpr` enables compile-time computation and better optimization.

---

=== Explain Rule of 3 / 5 / 0

If a class manages resources, it must define:

* Rule of 3: destructor, copy constructor, copy assignment
* Rule of 5: + move constructor, move assignment
* Rule of 0: use RAII types so no custom management is needed

Rule of 0 is preferred in modern C++.

---

== STL and Containers

=== Difference between vector and list

| vector | list |
|------|------|
| Contiguous memory | Non-contiguous |
| Fast random access | No random access |
| Cache friendly | Poor cache locality |
| Insertion costly in middle | Fast insertion |

Trade-off: choose `vector` for performance, `list` for frequent middle insertions.

---

=== map vs unordered_map

| map | unordered_map |
|----|---------------|
| Ordered | Unordered |
| O(log n) | O(1) average |
| Tree-based | Hash-based |

Trade-off:
* `map` when ordering is required
* `unordered_map` when lookup speed matters

---

=== Iterator invalidation – explain

Some STL operations invalidate iterators.

[source,cpp]
----
std::vector<int> v = {1,2,3};
auto it = v.begin();
v.push_back(4); // it may be invalid
----

Understanding iterator invalidation avoids subtle bugs.

---

== Memory Management

=== Stack vs Heap

* Stack: fast, automatic, limited size
* Heap: flexible, slower, manual or managed

[source,cpp]
----
int x = 10;        // stack
int* p = new int; // heap
delete p;
----

Low-latency systems prefer stack allocation when possible.

---

=== What is a memory leak?

Memory allocated but never released.

[source,cpp]
----
void leak() {
    int* p = new int(5);
}
----

Use smart pointers to avoid leaks.

---

=== What is a dangling pointer?

Pointer pointing to freed memory.

[source,cpp]
----
int* p = new int(10);
delete p;
p = nullptr;
----

Always nullify pointers after deletion.

---

== Smart Pointers

=== unique_ptr vs shared_ptr

* `unique_ptr`: exclusive ownership, zero overhead
* `shared_ptr`: reference-counted shared ownership

[source,cpp]
----
auto u = std::make_unique<int>(5);
auto s = std::make_shared<int>(10);
----

Prefer `unique_ptr` unless sharing is required.

---

== Multithreading & Concurrency

=== What is a race condition?

Occurs when multiple threads access shared data without synchronization.

[source,cpp]
----
int x = 0;
void f() { x++; } // unsafe
----

Must be protected using mutex or atomic operations.

---

=== mutex vs spinlock

* mutex: blocks thread, OS scheduling
* spinlock: busy-waits, no context switch

Trade-off:
* mutex → safer, slower under contention
* spinlock → faster for very short critical sections

---

=== What is a deadlock?

Threads wait indefinitely for each other.

Common causes:
* Circular wait
* Multiple locks in different order

Solution:
* Consistent lock ordering
* Scoped locking

---

=== What is a condition variable?

Used when a thread must wait for a condition to become true.

[source,cpp]
----
std::condition_variable cv;
----

Avoids busy-waiting.

---

== Low-Latency & System Design

=== What is latency vs throughput?

* Latency: time to process a single request
* Throughput: number of requests per second

Low-latency systems prioritize **predictability** over throughput.

---

=== Why avoid heap allocation in hot paths?

* Heap allocation is slow
* Causes latency jitter
* Involves locks internally

Trade-off: reduced flexibility for predictable performance.

---

=== TCP vs UDP

* TCP: reliable, ordered, higher latency
* UDP: fast, unordered, no guarantee

Trading systems often use UDP for market data.

---

== Linux & Debugging

=== How do you debug a crash?

* Check logs
* Analyze core dump
* Use gdb backtrace

[source,bash]
----
gdb ./app core
bt
----

---

=== How do you detect memory leaks?

* valgrind
* sanitizers
* code review and RAII usage

---

== Python & SQL (Support Skills)

=== Why Python is used with C++?

* Automation
* Testing
* Monitoring
* Tooling

Python is not used in latency-critical paths.

---

=== What is DDL?

DDL defines database schema.

[source,sql]
----
CREATE TABLE trades(id INT);
----

---

== Behavioral & Communication

=== How do you explain trade-offs?

A good answer explains:

* Options
* Pros and cons
* Reason for final choice

Example:
> “I chose unordered_map for faster lookup, trading ordering and memory usage for speed.”

---

== Final Interview Advice

SCB values:
* Clear thinking
* Safe code
* Trade-off awareness
* Communication clarity

They do NOT value:
* Memorized answers
* Overconfidence
* Guesswork

---

== Conclusion

Preparing these questions with **deep understanding** and **clear explanations** aligns strongly with SCB’s interview expectations for C++ and systems roles.
