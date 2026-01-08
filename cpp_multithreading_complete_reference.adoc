= C++ Multithreading Complete Reference (With Examples)
:toc:
:toclevels: 4
:sectnums:
:source-highlighter: coderay

== Introduction

Multithreading in C++ allows multiple threads of execution within a single process.
It is used to improve performance, responsiveness, and concurrency, especially on multi-core systems.

Modern C++ (C++11 and later) provides a standard threading library that abstracts platform-specific APIs.

---

== Basic Concepts

=== What Is a Thread?

A thread is the smallest unit of execution scheduled by the operating system.
Multiple threads share:
* Same address space
* Same heap
* Same global variables

Each thread has:
* Its own stack
* Its own instruction pointer

---

=== Process vs Thread

| Process | Thread |
|------|-------|
| Own address space | Shared address space |
| Heavyweight | Lightweight |
| Slow creation | Fast creation |
| Strong isolation | Requires synchronization |

---

== std::thread

=== Creating a Thread

[source,cpp]
----
#include <thread>
#include <iostream>

void task() {
    std::cout << "Thread running\n";
}

int main() {
    std::thread t(task);
    t.join();
}
----

A thread starts executing immediately after construction.

---

=== join() vs detach()

* `join()` blocks until thread finishes
* `detach()` runs thread independently

[source,cpp]
----
std::thread t(task);
t.detach(); // no join possible after this
----

Detached threads must not access destroyed objects.

---

== Passing Arguments to Threads

[source,cpp]
----
void print(int x) {
    std::cout << x << std::endl;
}

std::thread t(print, 10);
t.join();
----

---

=== Passing by Reference

[source,cpp]
----
void increment(int& x) {
    x++;
}

int value = 0;
std::thread t(increment, std::ref(value));
t.join();
----

`std::ref` is required to avoid copying.

---

== Race Conditions

=== What Is a Race Condition?

Occurs when:
* Multiple threads access shared data
* At least one write occurs
* No synchronization is used

[source,cpp]
----
int counter = 0;

void inc() {
    counter++; // unsafe
}
----

Result is undefined.

---

== Mutex

=== std::mutex

Mutex ensures mutual exclusion.

[source,cpp]
----
#include <mutex>

std::mutex m;
int counter = 0;

void inc() {
    std::lock_guard<std::mutex> lock(m);
    counter++;
}
----

---

=== lock_guard vs unique_lock

| lock_guard | unique_lock |
|----------|------------|
| Simple | Flexible |
| RAII only | Can unlock/lock |
| Faster | Slight overhead |

---

== Deadlock

=== What Is Deadlock?

Threads wait indefinitely due to circular locking.

[source,cpp]
----
std::mutex m1, m2;

void f1() {
    std::lock_guard<std::mutex> l1(m1);
    std::lock_guard<std::mutex> l2(m2);
}

void f2() {
    std::lock_guard<std::mutex> l2(m2);
    std::lock_guard<std::mutex> l1(m1);
}
----

---

=== Avoiding Deadlock

* Lock in consistent order
* Use `std::lock`
* Minimize lock scope

[source,cpp]
----
std::lock(m1, m2);
std::lock_guard<std::mutex> l1(m1, std::adopt_lock);
std::lock_guard<std::mutex> l2(m2, std::adopt_lock);
----

---

== Condition Variables

=== Why Condition Variables?

Used when a thread must wait until a condition becomes true.

Avoids busy-waiting.

---

=== Example: Producer-Consumer

[source,cpp]
----
#include <condition_variable>
#include <queue>

std::mutex m;
std::condition_variable cv;
std::queue<int> q;

void producer() {
    std::lock_guard<std::mutex> lock(m);
    q.push(1);
    cv.notify_one();
}

void consumer() {
    std::unique_lock<std::mutex> lock(m);
    cv.wait(lock, []{ return !q.empty(); });
    q.pop();
}
----

---

== Atomic Operations

=== std::atomic

Used for lock-free operations.

[source,cpp]
----
#include <atomic>

std::atomic<int> counter{0};

void inc() {
    counter++;
}
----

---

=== atomic vs mutex

| atomic | mutex |
|------|-------|
| Lock-free | Lock-based |
| Fast | Safer for complex logic |
| Limited operations | General purpose |

---

== Memory Visibility and Ordering

=== Memory Reordering

Compilers and CPUs may reorder instructions for optimization.
Synchronization enforces visibility.

[source,cpp]
----
std::atomic<int> x{0};
x.store(1, std::memory_order_release);
----

---

== Thread Safety Levels

| Level | Meaning |
|----|-------|
| Not thread-safe | No protection |
| Thread-compatible | Caller synchronizes |
| Thread-safe | Internal synchronization |

---

== Thread Pool (Concept)

Creating threads repeatedly is expensive.
Thread pools reuse threads.

[source,text]
----
Task Queue -> Worker Threads -> Execute Tasks
----

Thread pools improve scalability.

---

== Thread Lifetime Issues

=== Accessing Destroyed Objects

[source,cpp]
----
void run() {
    int x = 10;
    std::thread t([&]{ std::cout << x; });
    t.detach(); // dangerous
}
----

This leads to undefined behavior.

---

== Performance Considerations

* Avoid locking hot paths
* Prefer stack allocation
* Avoid false sharing
* Minimize context switches

---

== Low-Latency Trade-offs

| Choice | Gain | Cost |
|-----|------|-----|
| Mutex | Safety | Latency |
| Spinlock | Speed | CPU burn |
| Atomic | Fast | Limited |
| Lock-free | Scalability | Complexity |

---

== Common Interview Questions

=== What happens if a thread is not joined or detached?

Calling destructor on a joinable thread calls `std::terminate`.

---

=== Is std::mutex recursive?

No. Use `std::recursive_mutex` if needed.

---

=== Can mutex be copied?

No. Mutexes are non-copyable.

---

== Best Practices

* Always use RAII locks
* Prefer `lock_guard`
* Avoid detached threads
* Keep critical sections small
* Use atomics for counters only
* Measure before optimizing

---

== Interview-Grade Explanation

> Multithreading in C++ is built around `std::thread`, synchronization primitives, and strict memory visibility rules.  
> Correctness comes first, then performance.  
> All threading designs involve trade-offs between safety, latency, and complexity.

---

== One-Line Takeaway

C++ multithreading provides powerful low-level control, but requires disciplined design, proper synchronization, and deep understanding of object lifetimes and memory visibility.

