# Multithreading (std::thread, pthreads, Boost.Thread)

This file combines the following references into one document:

1. [C++ Multithreading Complete Reference (With Examples)](#part-1) — from `cpp_multithreading_complete_reference.md`
2. [C++ Multithreading — Complete Reference with Examples](#part-2) — from `cpp_multithreading_complete_reference_with_example.md`
3. [POSIX Threads (pthreads) – Complete Multithreading Reference with Examples](#part-3) — from `pthread_multithreading_complete_reference.md`
4. [pthread vs std::thread vs Boost.Thread — Complete Reference](#part-4) — from `pthread_vs_std_thread_vs_boost_thread.md`

---

<a id="part-1"></a>

*Part 1 — source: `cpp_multithreading_complete_reference.md`*

## C++ Multithreading Complete Reference (With Examples)

### Introduction

Multithreading in C++ allows multiple threads of execution within a single process.
It is used to improve performance, responsiveness, and concurrency, especially on multi-core systems.

Modern C++ (C++11 and later) provides a standard threading library that abstracts platform-specific APIs.

---

### Basic Concepts

#### What Is a Thread?

A thread is the smallest unit of execution scheduled by the operating system.
Multiple threads share:
- Same address space
- Same heap
- Same global variables

Each thread has:
- Its own stack
- Its own instruction pointer

---

#### Process vs Thread

| Process | Thread |
|------|-------|
| Own address space | Shared address space |
| Heavyweight | Lightweight |
| Slow creation | Fast creation |
| Strong isolation | Requires synchronization |

---

### std::thread

#### Creating a Thread

```cpp
#include <thread>
#include <iostream>

void task() {
    std::cout << "Thread running\n";
}

int main() {
    std::thread t(task);
    t.join();
}
```

A thread starts executing immediately after construction.

---

#### join() vs detach()

- `join()` blocks until thread finishes
- `detach()` runs thread independently

```cpp
std::thread t(task);
t.detach(); // no join possible after this
```

Detached threads must not access destroyed objects.

---

### Passing Arguments to Threads

```cpp
void print(int x) {
    std::cout << x << std::endl;
}

std::thread t(print, 10);
t.join();
```

---

#### Passing by Reference

```cpp
void increment(int& x) {
    x++;
}

int value = 0;
std::thread t(increment, std::ref(value));
t.join();
```

`std::ref` is required to avoid copying.

---

### Race Conditions

#### What Is a Race Condition?

Occurs when:
- Multiple threads access shared data
- At least one write occurs
- No synchronization is used

```cpp
int counter = 0;

void inc() {
    counter++; // unsafe
}
```

Result is undefined.

---

### Mutex

#### std::mutex

Mutex ensures mutual exclusion.

```cpp
#include <mutex>

std::mutex m;
int counter = 0;

void inc() {
    std::lock_guard<std::mutex> lock(m);
    counter++;
}
```

---

#### lock_guard vs unique_lock

| lock_guard | unique_lock |
|----------|------------|
| Simple | Flexible |
| RAII only | Can unlock/lock |
| Faster | Slight overhead |

---

### Deadlock

#### What Is Deadlock?

Threads wait indefinitely due to circular locking.

```cpp
std::mutex m1, m2;

void f1() {
    std::lock_guard<std::mutex> l1(m1);
    std::lock_guard<std::mutex> l2(m2);
}

void f2() {
    std::lock_guard<std::mutex> l2(m2);
    std::lock_guard<std::mutex> l1(m1);
}
```

---

#### Avoiding Deadlock

- Lock in consistent order
- Use `std::lock`
- Minimize lock scope

```cpp
std::lock(m1, m2);
std::lock_guard<std::mutex> l1(m1, std::adopt_lock);
std::lock_guard<std::mutex> l2(m2, std::adopt_lock);
```

---

### Condition Variables

#### Why Condition Variables?

Used when a thread must wait until a condition becomes true.

Avoids busy-waiting.

---

#### Example: Producer-Consumer

```cpp
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
```

---

### Atomic Operations

#### std::atomic

Used for lock-free operations.

```cpp
#include <atomic>

std::atomic<int> counter{0};

void inc() {
    counter++;
}
```

---

#### atomic vs mutex

| atomic | mutex |
|------|-------|
| Lock-free | Lock-based |
| Fast | Safer for complex logic |
| Limited operations | General purpose |

---

### Memory Visibility and Ordering

#### Memory Reordering

Compilers and CPUs may reorder instructions for optimization.
Synchronization enforces visibility.

```cpp
std::atomic<int> x{0};
x.store(1, std::memory_order_release);
```

---

### Thread Safety Levels

| Level | Meaning |
|----|-------|
| Not thread-safe | No protection |
| Thread-compatible | Caller synchronizes |
| Thread-safe | Internal synchronization |

---

### Thread Pool (Concept)

Creating threads repeatedly is expensive.
Thread pools reuse threads.

```text
Task Queue -> Worker Threads -> Execute Tasks
```

Thread pools improve scalability.

---

### Thread Lifetime Issues

#### Accessing Destroyed Objects

```cpp
void run() {
    int x = 10;
    std::thread t([&]{ std::cout << x; });
    t.detach(); // dangerous
}
```

This leads to undefined behavior.

---

### Performance Considerations

- Avoid locking hot paths
- Prefer stack allocation
- Avoid false sharing
- Minimize context switches

---

### Low-Latency Trade-offs

| Choice | Gain | Cost |
|-----|------|-----|
| Mutex | Safety | Latency |
| Spinlock | Speed | CPU burn |
| Atomic | Fast | Limited |
| Lock-free | Scalability | Complexity |

---

### Common Interview Questions

#### What happens if a thread is not joined or detached?

Calling destructor on a joinable thread calls `std::terminate`.

---

#### Is std::mutex recursive?

No. Use `std::recursive_mutex` if needed.

---

#### Can mutex be copied?

No. Mutexes are non-copyable.

---

### Best Practices

- Always use RAII locks
- Prefer `lock_guard`
- Avoid detached threads
- Keep critical sections small
- Use atomics for counters only
- Measure before optimizing

---

### Interview-Grade Explanation

> Multithreading in C++ is built around `std::thread`, synchronization primitives, and strict memory visibility rules.
> Correctness comes first, then performance.
> All threading designs involve trade-offs between safety, latency, and complexity.

---

### One-Line Takeaway

C++ multithreading provides powerful low-level control, but requires disciplined design, proper synchronization, and deep understanding of object lifetimes and memory visibility.

---

<a id="part-2"></a>

*Part 2 — source: `cpp_multithreading_complete_reference_with_example.md`*

## C++ Multithreading — Complete Reference with Examples

### 1. Introduction

Multithreading allows a program to execute **multiple tasks concurrently**.

In C++ (C++11+), threading is part of the **standard library** and provides:
- Portable threads
- Synchronization primitives
- Atomic operations
- Memory visibility guarantees

Used in:
- Servers
- Trading systems
- Parallel computation
- I/O systems

---

### 2. Ways to Create Threads in C++

---

#### 2.1 Using a Free Function

```cpp
#include <thread>

void task() {
    std::cout << "Hello from thread\n";
}

int main() {
    std::thread t(task);
    t.join();
}
```

---

#### 2.2 Using a Lambda

```cpp
std::thread t([]{
    std::cout << "Lambda thread\n";
});
t.join();
```

Most commonly used in modern C++.

---

#### 2.3 Using a Functor (Function Object)

```cpp
struct Worker {
    void operator()() {
        std::cout << "Functor thread\n";
    }
};

std::thread t(Worker{});
t.join();
```

---

#### 2.4 Using a Member Function

```cpp
class Job {
public:
    void run() {
        std::cout << "Member function thread\n";
    }
};

Job j;
std::thread t(&Job::run, &j);
t.join();
```

---

#### 2.5 Passing Arguments to Threads

```cpp
void print(int x) {
    std::cout << x << "\n";
}

std::thread t(print, 10);
t.join();
```

Pass by reference:

```cpp
std::thread t(print, std::ref(x));
```

---

### 3. Thread Lifecycle

- Create → Running → Finished
- Must call `join()` or `detach()`

```cpp
t.join();    // wait for thread
t.detach();  // run independently
```

Destroying a joinable thread calls `std::terminate`.

---

### 4. What Is Thread Safety?

Thread safety means:
> Correct behavior when multiple threads access shared data concurrently.

Problems arise due to:
- Data races
- Lost updates
- Inconsistent state

---

### 5. Data Race Example (Unsafe)

```cpp
int counter = 0;

void inc() {
    counter++;   // NOT thread-safe
}
```

Multiple threads incrementing `counter` cause undefined behavior.

---

### 6. Making Code Thread-Safe

---

#### 6.1 Using Mutex

```cpp
#include <mutex>

int counter = 0;
std::mutex m;

void inc() {
    std::lock_guard<std::mutex> lock(m);
    counter++;
}
```

Ensures mutual exclusion.

---

#### 6.2 Using std::atomic

```cpp
#include <atomic>

std::atomic<int> counter{0};

void inc() {
    counter++;
}
```

Lock-free, faster for simple operations.

---

### 7. Famous Multithreading Problems

---

### 7.1 Odd–Even Printing (Classic Interview Problem)

Print numbers in order using two threads.

```cpp
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
```

Concepts used:
- Mutex
- Condition variable
- Signaling

---

### 7.2 Producer–Consumer Problem

One thread produces data, another consumes it.

```cpp
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
```

---

### 7.3 Thread-Safe Counter

```cpp
class Counter {
    std::atomic<int> value{0};
public:
    void increment() { value++; }
    int get() const { return value.load(); }
};
```

---

### 7.4 Reader–Writer Problem (Simplified)

Multiple readers, single writer.

```cpp
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
```

---

### 7.5 Dining Philosophers (Simplified)

Avoid deadlock by locking in order.

```cpp
std::mutex forks[5];

void eat(int i) {
    std::lock(forks[i], forks[(i+1)%5]);
    std::lock_guard<std::mutex> l1(forks[i], std::adopt_lock);
    std::lock_guard<std::mutex> l2(forks[(i+1)%5], std::adopt_lock);
    std::cout << "Eating " << i << "\n";
}
```

---

### 8. Condition Variables

Used for **thread coordination**, not mutual exclusion.

```cpp
cv.wait(lock, predicate);
cv.notify_one();
cv.notify_all();
```

Always use with a predicate.

---

### 9. Deadlock

Occurs when threads wait on each other forever.

```cpp
std::mutex m1, m2;
```

Solution:
- Lock ordering
- std::lock
- Avoid nested locks

---

### 10. Thread Pool (Basic Idea)

Reuse threads instead of creating repeatedly.

```cpp
class ThreadPool {
    std::vector<std::thread> workers;
};
```

Used in:
- Servers
- Task schedulers

---

### 11. Thread Safety Rules (Golden Rules)

- Minimize shared state
- Prefer immutability
- Use RAII locks
- Prefer std::atomic when possible
- Avoid holding locks long
- Always join threads

---

### 12. Common Threading Mistakes

- Forgetting join/detach
- Data races
- Deadlocks
- Overusing mutexes
- Using std::function in hot paths
- Ignoring false sharing

---

### 13. Performance Considerations

- Threads are expensive
- Context switching is costly
- Cache contention hurts performance
- Lock-free is not always faster

---

### 14. Interview-Famous Threading Questions

- Odd–Even printing
- Producer–Consumer
- Thread-safe singleton
- Deadlock scenarios
- Atomic vs mutex

---

### 15. Final Takeaway

C++ multithreading provides powerful tools, but correctness is more important than speed.

Correct multithreaded code requires:
- Discipline
- Understanding of synchronization
- Careful design

This document is a **complete practical reference**.

---

<a id="part-3"></a>

*Part 3 — source: `pthread_multithreading_complete_reference.md`*

## POSIX Threads (pthreads) – Complete Multithreading Reference with Examples

### Introduction

POSIX Threads (pthreads) is a low-level threading API provided by POSIX-compliant operating systems.
It is widely used in system programming, high-performance systems, and legacy C/C++ codebases.

Unlike std::thread, pthreads require explicit management of:
- Thread lifecycle
- Synchronization
- Memory visibility
- Error handling

Understanding pthreads demonstrates strong system-level knowledge.

---

### Thread Basics

#### What Is a pthread?

A pthread represents a lightweight thread of execution within a process.
All threads share:
- Address space
- Heap
- Global variables

Each thread has:
- Its own stack
- Its own registers

---

### Creating Threads

#### pthread_create

```c
#include <pthread.h>
#include <stdio.h>

void* worker(void* arg) {
    printf("Thread running\n");
    return NULL;
}

int main() {
    pthread_t thread;
    pthread_create(&thread, NULL, worker, NULL);
    pthread_join(thread, NULL);
    return 0;
}
```

Key points:
- Thread starts immediately after creation
- Function must return void*
- Arguments passed via void*

---

### Joining vs Detaching Threads

#### pthread_join

Blocks until the thread exits.

```c
pthread_join(thread, NULL);
```

---

#### pthread_detach

Thread resources released automatically on exit.

```c
pthread_detach(thread);
```

Detached threads must never access objects that may go out of scope.

---

### Passing Arguments to Threads

```c
void* print_value(void* arg) {
    int value = *(int*)arg;
    printf("%d\n", value);
    return NULL;
}

int main() {
    int x = 10;
    pthread_t t;
    pthread_create(&t, NULL, print_value, &x);
    pthread_join(t, NULL);
}
```

Caller must ensure argument lifetime.

---

### Race Conditions

#### Unsafe Shared Access

```c
int counter = 0;

void* increment(void*) {
    counter++;  // race condition
    return NULL;
}
```

Multiple threads updating shared data without synchronization leads to undefined behavior.

---

### Mutex

#### pthread_mutex_t

Mutex provides mutual exclusion.

```c
#include <pthread.h>

pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;
int counter = 0;

void* increment(void*) {
    pthread_mutex_lock(&mutex);
    counter++;
    pthread_mutex_unlock(&mutex);
    return NULL;
}
```

---

#### Mutex Initialization

```c
pthread_mutex_t m;
pthread_mutex_init(&m, NULL);
```

Always destroy mutex when done:

```c
pthread_mutex_destroy(&m);
```

---

### Deadlock

#### Deadlock Example

```c
pthread_mutex_t m1, m2;

void* f1(void*) {
    pthread_mutex_lock(&m1);
    pthread_mutex_lock(&m2);
    return NULL;
}

void* f2(void*) {
    pthread_mutex_lock(&m2);
    pthread_mutex_lock(&m1);
    return NULL;
}
```

Deadlock occurs due to inconsistent lock ordering.

---

#### Avoiding Deadlock

- Enforce consistent lock order
- Use trylock
- Reduce lock scope

```c
if (pthread_mutex_trylock(&m1) == 0) {
    pthread_mutex_trylock(&m2);
}
```

---

### Condition Variables

#### Why Condition Variables?

Used to block threads until a condition becomes true.
Avoids busy-waiting.

---

#### Producer-Consumer Example

```c
#include <pthread.h>
#include <stdio.h>

pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;
pthread_cond_t cond = PTHREAD_COND_INITIALIZER;
int data_ready = 0;

void* producer(void*) {
    pthread_mutex_lock(&mutex);
    data_ready = 1;
    pthread_cond_signal(&cond);
    pthread_mutex_unlock(&mutex);
    return NULL;
}

void* consumer(void*) {
    pthread_mutex_lock(&mutex);
    while (!data_ready)
        pthread_cond_wait(&cond, &mutex);
    printf("Consumed\n");
    pthread_mutex_unlock(&mutex);
    return NULL;
}
```

---

### Thread Cancellation

#### pthread_cancel

```c
pthread_cancel(thread);
```

Thread must reach a cancellation point.

---

### Thread-Specific Data (TLS)

#### Thread-Local Storage

```c
pthread_key_t key;

void destructor(void* value) {
    free(value);
}

pthread_key_create(&key, destructor);

void* thread_func(void*) {
    int* data = malloc(sizeof(int));
    *data = 10;
    pthread_setspecific(key, data);
    return NULL;
}
```

Each thread gets its own data copy.

---

### Atomic Operations (pthreads + GCC)

Atomic operations prevent race conditions without mutexes.

```c
__sync_fetch_and_add(&counter, 1);
```

Preferred modern approach: C11 atomics.

---

### Memory Visibility

pthread mutex and condition variables enforce memory barriers.
Unlocking a mutex guarantees visibility to the next locking thread.

---

### Performance Considerations

- Mutex locking is expensive
- Context switches cost CPU cycles
- Avoid fine-grained locking
- Avoid false sharing

---

### pthread vs std::thread

| Feature | pthread | std::thread |
|------|--------|------------|
| Abstraction | Low-level | High-level |
| Portability | POSIX only | Cross-platform |
| Error handling | Manual | RAII |
| Safety | Programmer-managed | Safer |

pthreads offer more control but require discipline.

---

### Low-Latency Trade-offs

| Choice | Gain | Cost |
|-----|------|-----|
| Mutex | Safety | Latency |
| Spinlock | Speed | CPU burn |
| Lock-free | Scalability | Complexity |
| TLS | Isolation | Memory |

---

### Common Interview Questions

#### What happens if mutex is not unlocked?

Deadlock or resource starvation.

---

#### Can mutex be copied?

No. pthread mutexes are non-copyable.

---

#### Why use condition variables instead of sleep?

sleep wastes CPU and adds latency.

---

### Best Practices

- Always check return codes
- Use mutex + condition variables together
- Avoid detached threads
- Minimize shared state
- Destroy synchronization objects
- Prefer std::thread unless pthread control is required

---

### Interview-Grade Explanation

> pthreads provide low-level control over thread creation, synchronization, and memory visibility.
> Correct usage requires explicit lifecycle management and strict discipline to avoid race conditions, deadlocks, and undefined behavior.

---

### One-Line Takeaway

POSIX Threads offer powerful, low-level concurrency control but demand precise design, careful synchronization, and deep understanding of thread lifetimes.

---

<a id="part-4"></a>

*Part 4 — source: `pthread_vs_std_thread_vs_boost_thread.md`*

## pthread vs std::thread vs Boost.Thread — Complete Reference

### Introduction

C++ supports multithreading through multiple libraries and abstractions.
The most commonly encountered are:

- POSIX Threads (pthread)
- C++ Standard Library threads (std::thread)
- Boost.Thread

Understanding the differences, trade-offs, and use cases of each is critical for
system programming, low-latency systems, and technical interviews.

---

### pthread (POSIX Threads)

#### What is pthread?

pthread is a low-level threading API defined by the POSIX standard.
It is written in C and directly interfaces with the operating system’s threading model.

On Linux, pthread maps closely to kernel threads.

---

#### Key Characteristics

- C-based API
- Manual lifecycle management
- Explicit error handling
- POSIX-only (Linux, Unix)
- Maximum control over threading behavior

---

#### Basic Example

```c
#include <pthread.h>
#include <stdio.h>

void* worker(void*) {
    printf("pthread running\n");
    return NULL;
}

int main() {
    pthread_t t;
    pthread_create(&t, NULL, worker, NULL);
    pthread_join(t, NULL);
    return 0;
}
```

---

#### Advantages of pthread

- Fine-grained OS control
- Thread attributes (stack size, scheduling policy)
- Thread affinity and priority
- Integration with C libraries
- Suitable for OS-level and infrastructure code

---

#### Disadvantages of pthread

- Verbose and error-prone
- No RAII
- Manual resource cleanup
- Harder to maintain
- Easy to introduce subtle bugs

---

### std::thread (C++ Standard Thread)

#### What is std::thread?

`std::thread` is the standard C++ threading abstraction introduced in C++11.
It provides a safer, higher-level interface built on top of OS threads.

On Linux, `std::thread` internally uses pthread.

---

#### Key Characteristics

- C++ abstraction
- RAII-based
- Exception-safe
- Portable across platforms
- Integrates with C++ features

---

#### Basic Example

```cpp
#include <thread>
#include <iostream>

void worker() {
    std::cout << "std::thread running\n";
}

int main() {
    std::thread t(worker);
    t.join();
}
```

---

#### Advantages of std::thread

- Automatic resource management (RAII)
- Cleaner syntax
- Cross-platform portability
- Safer default behavior
- Standardized and future-proof

---

#### Disadvantages of std::thread

- Less direct OS-level control
- No built-in thread affinity API
- Slight abstraction overhead (usually negligible)

---

#### Important Destructor Rule

If a `std::thread` object is destroyed while still joinable:

```cpp
std::terminate();
```

This forces developers to explicitly call `join()` or `detach()`.

---

### Boost.Thread

#### What is Boost.Thread?

Boost.Thread is a threading library from the Boost C++ Libraries.
It predates C++11 and heavily influenced the design of `std::thread`.

---

#### Historical Context

Timeline:

```text
Boost.Thread  -->  C++11 std::thread
```

Many Boost.Thread concepts were standardized into C++.

---

#### Basic Example

```cpp
#include <boost/thread.hpp>
#include <iostream>

void worker() {
    std::cout << "boost::thread running\n";
}

int main() {
    boost::thread t(worker);
    t.join();
}
```

---

#### Advantages of Boost.Thread

- RAII-based
- Rich feature set (older compilers)
- Familiar API similar to std::thread

---

#### Disadvantages of Boost.Thread

- External dependency
- Not part of C++ standard
- Largely obsolete for new code
- std::thread preferred in modern C++

---

### Feature Comparison

| Feature | pthread | std::thread | Boost.Thread |
|------|--------|------------|-------------|
| Language | C | C++ | C++ |
| Level | Low-level | High-level | High-level |
| RAII | No | Yes | Yes |
| Portability | POSIX only | Cross-platform | Cross-platform |
| OS Control | Full | Limited | Limited |
| Standard | No | Yes | No |
| Modern Usage | Legacy/System | Preferred | Legacy |

---

### Performance Reality (Linux)

On Linux systems:

```text
std::thread
  ↓
libstdc++
  ↓
pthread
  ↓
Linux kernel
```

Important points:
- std::thread uses pthread internally
- No meaningful performance penalty
- Safety and portability are gained

---

### When to Use Which?

#### Use pthread when:

- You need thread affinity or scheduling control
- You are writing OS-level or infrastructure code
- You integrate with C libraries
- You maintain legacy systems

---

#### Use std::thread when:

- Writing modern C++ (C++11+)
- Building application or system-level code
- Portability and safety matter
- Maintainability is important

---

#### Use Boost.Thread when:

- Supporting pre-C++11 compilers
- Maintaining legacy Boost-based systems

For new code, Boost.Thread is generally not recommended.

---

### Interview-Grade Explanation

A strong interview answer:

> “I prefer std::thread for modern C++ because it’s portable, RAII-based, and safer.
> On Linux it uses pthread internally, so there’s no performance loss.
> I use pthread only when I need OS-level control like affinity or when integrating with C libraries.
> Boost.Thread was a precursor to std::thread and is mostly obsolete for new code.”

---

### Common Interview Questions

#### Is std::thread slower than pthread?

No. On Linux, std::thread uses pthread internally.

---

#### Is Boost.Thread the same as std::thread?

Conceptually yes, but std::thread is standardized and preferred.

---

#### Why does std::thread destructor call terminate?

To enforce explicit thread lifecycle management and avoid silent bugs.

---

### Trade-off Summary

| Choice | Gain | Cost |
|------|------|-----|
| pthread | Control | Safety, complexity |
| std::thread | Safety, portability | Less OS control |
| Boost.Thread | Compatibility | External dependency |

---

### One-Line Takeaway

pthread gives control, std::thread gives safety, and Boost.Thread was the bridge between them.
