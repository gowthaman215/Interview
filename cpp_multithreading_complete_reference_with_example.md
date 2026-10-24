= C++ Multithreading — Complete Reference with Examples
:toc:
:toclevels: 4
:sectnums:
:source-highlighter: coderay

== 1. Introduction

Multithreading allows a program to execute **multiple tasks concurrently**.

In C++ (C++11+), threading is part of the **standard library** and provides:
* Portable threads
* Synchronization primitives
* Atomic operations
* Memory visibility guarantees

Used in:
* Servers
* Trading systems
* Parallel computation
* I/O systems

---

== 2. Ways to Create Threads in C++

---

=== 2.1 Using a Free Function

[source,cpp]
----
#include <thread>

void task() {
    std::cout << "Hello from thread\n";
}

int main() {
    std::thread t(task);
    t.join();
}
----

---

=== 2.2 Using a Lambda

[source,cpp]
----
std::thread t([]{
    std::cout << "Lambda thread\n";
});
t.join();
----

Most commonly used in modern C++.

---

=== 2.3 Using a Functor (Function Object)

[source,cpp]
----
struct Worker {
    void operator()() {
        std::cout << "Functor thread\n";
    }
};

std::thread t(Worker{});
t.join();
----

---

=== 2.4 Using a Member Function

[source,cpp]
----
class Job {
public:
    void run() {
        std::cout << "Member function thread\n";
    }
};

Job j;
std::thread t(&Job::run, &j);
t.join();
----

---

=== 2.5 Passing Arguments to Threads

[source,cpp]
----
void print(int x) {
    std::cout << x << "\n";
}

std::thread t(print, 10);
t.join();
----

Pass by reference:

[source,cpp]
----
std::thread t(print, std::ref(x));
----

---

== 3. Thread Lifecycle

* Create → Running → Finished
* Must call `join()` or `detach()`

[source,cpp]
----
t.join();    // wait for thread
t.detach();  // run independently
----

Destroying a joinable thread calls `std::terminate`.

---

== 4. What Is Thread Safety?

Thread safety means:
> Correct behavior when multiple threads access shared data concurrently.

Problems arise due to:
* Data races
* Lost updates
* Inconsistent state

---

== 5. Data Race Example (Unsafe)

[source,cpp]
----
int counter = 0;

void inc() {
    counter++;   // NOT thread-safe
}
----

Multiple threads incrementing `counter` cause undefined behavior.

---

== 6. Making Code Thread-Safe

---

=== 6.1 Using Mutex

[source,cpp]
----
#include <mutex>

int counter = 0;
std::mutex m;

void inc() {
    std::lock_guard<std::mutex> lock(m);
    counter++;
}
----

Ensures mutual exclusion.

---

=== 6.2 Using std::atomic

[source,cpp]
----
#include <atomic>

std::atomic<int> counter{0};

void inc() {
    counter++;
}
----

Lock-free, faster for simple operations.

---

== 7. Famous Multithreading Problems

---

== 7.1 Odd–Even Printing (Classic Interview Problem)

Print numbers in order using two threads.

[source,cpp]
----
#include <thread>
#include <mutex>
#include <condition_variable>

int n = 10;
int current = 1;
std::mutex m;
std::condition_variable cv;

void print_odd() {
    while (current <= n) {
        std::unique_lock<std::mutex> lock(m);
        cv.wait(lock, []{ return current % 2 == 1; });
        if (current <= n) {
            std::cout << current++ << " ";
        }
        cv.notify_all();
    }
}

void print_even() {
    while (current <= n) {
        std::unique_lock<std::mutex> lock(m);
        cv.wait(lock, []{ return current % 2 == 0; });
        if (current <= n) {
            std::cout << current++ << " ";
        }
        cv.notify_all();
    }
}

int main() {
    std::thread t1(print_odd);
    std::thread t2(print_even);
    t1.join();
    t2.join();
}
----

Concepts used:
* Mutex
* Condition variable
* Signaling

---

== 7.2 Producer–Consumer Problem

One thread produces data, another consumes it.

[source,cpp]
----
#include <queue>
#include <thread>
#include <mutex>
#include <condition_variable>

std::queue<int> buffer;
const int MAX = 5;
std::mutex m;
std::condition_variable cv;

void producer() {
    for (int i = 0; i < 10; i++) {
        std::unique_lock<std::mutex> lock(m);
        cv.wait(lock, []{ return buffer.size() < MAX; });
        buffer.push(i);
        std::cout << "Produced " << i << "\n";
        cv.notify_all();
    }
}

void consumer() {
    for (int i = 0; i < 10; i++) {
        std::unique_lock<std::mutex> lock(m);
        cv.wait(lock, []{ return !buffer.empty(); });
        int val = buffer.front();
        buffer.pop();
        std::cout << "Consumed " << val << "\n";
        cv.notify_all();
    }
}

int main() {
    std::thread p(producer);
    std::thread c(consumer);
    p.join();
    c.join();
}
----

---

== 7.3 Thread-Safe Counter

[source,cpp]
----
class Counter {
    std::atomic<int> value{0};
public:
    void increment() { value++; }
    int get() const { return value.load(); }
};
----

---

== 7.4 Reader–Writer Problem (Simplified)

Multiple readers, single writer.

[source,cpp]
----
#include <shared_mutex>

int data = 0;
std::shared_mutex sm;

void reader() {
    std::shared_lock lock(sm);
    std::cout << data << "\n";
}

void writer() {
    std::unique_lock lock(sm);
    data++;
}
----

---

== 7.5 Dining Philosophers (Simplified)

Avoid deadlock by locking in order.

[source,cpp]
----
std::mutex forks[5];

void eat(int i) {
    std::lock(forks[i], forks[(i+1)%5]);
    std::lock_guard<std::mutex> l1(forks[i], std::adopt_lock);
    std::lock_guard<std::mutex> l2(forks[(i+1)%5], std::adopt_lock);
    std::cout << "Eating " << i << "\n";
}
----

---

== 8. Condition Variables

Used for **thread coordination**, not mutual exclusion.

[source,cpp]
----
cv.wait(lock, predicate);
cv.notify_one();
cv.notify_all();
----

Always use with a predicate.

---

== 9. Deadlock

Occurs when threads wait on each other forever.

[source,cpp]
----
std::mutex m1, m2;
----

Solution:
* Lock ordering
* std::lock
* Avoid nested locks

---

== 10. Thread Pool (Basic Idea)

Reuse threads instead of creating repeatedly.

[source,cpp]
----
class ThreadPool {
    std::vector<std::thread> workers;
};
----

Used in:
* Servers
* Task schedulers

---

== 11. Thread Safety Rules (Golden Rules)

* Minimize shared state
* Prefer immutability
* Use RAII locks
* Prefer std::atomic when possible
* Avoid holding locks long
* Always join threads

---

== 12. Common Threading Mistakes

* Forgetting join/detach
* Data races
* Deadlocks
* Overusing mutexes
* Using std::function in hot paths
* Ignoring false sharing

---

== 13. Performance Considerations

* Threads are expensive
* Context switching is costly
* Cache contention hurts performance
* Lock-free is not always faster

---

== 14. Interview-Famous Threading Questions

* Odd–Even printing
* Producer–Consumer
* Thread-safe singleton
* Deadlock scenarios
* Atomic vs mutex

---

== 15. Final Takeaway

C++ multithreading provides powerful tools, but correctness is more important than speed.

Correct multithreaded code requires:
* Discipline
* Understanding of synchronization
* Careful design

This document is a **complete practical reference**.

