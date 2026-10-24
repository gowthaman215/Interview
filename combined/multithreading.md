# Multithreading (std::thread, pthreads, Boost.Thread)

A single, de-duplicated reference merged from:
`cpp_multithreading_complete_reference.md`, `cpp_multithreading_complete_reference_with_example.md`,
`pthread_multithreading_complete_reference.md` and `pthread_vs_std_thread_vs_boost_thread.md`.

## Contents

1. [Introduction](#introduction)
2. [Basic Concepts](#basic-concepts)
3. [std::thread](#stdthread)
4. [Synchronization in Standard C++](#synchronization-in-standard-c)
5. [Classic Multithreading Problems](#classic-multithreading-problems)
6. [POSIX Threads (pthreads)](#posix-threads-pthreads)
7. [Boost.Thread](#boostthread)
8. [pthread vs std::thread vs Boost.Thread](#pthread-vs-stdthread-vs-boostthread)
9. [Performance and Low-Latency Trade-offs](#performance-and-low-latency-trade-offs)
10. [Best Practices and Common Mistakes](#best-practices-and-common-mistakes)
11. [Common Interview Questions](#common-interview-questions)
12. [Interview-Grade Explanations](#interview-grade-explanations)
13. [Final Takeaway](#final-takeaway)

---

## Introduction

Multithreading allows a program to execute **multiple tasks concurrently** within a single process.
It is used to improve performance, responsiveness, and concurrency, especially on multi-core systems.

Used in:
- Servers
- Trading systems
- Parallel computation
- I/O systems

C++ supports multithreading through several libraries:

- C++ Standard Library threads (`std::thread`, C++11+) — portable threads, synchronization primitives, atomic operations and memory visibility guarantees, abstracting platform-specific APIs
- POSIX Threads (pthreads) — a low-level C API used in system programming, high-performance systems, and legacy C/C++ codebases
- Boost.Thread — the precursor to `std::thread`

Understanding the differences, trade-offs, and use cases of each is critical for system programming, low-latency systems, and technical interviews.

---

## Basic Concepts

### What Is a Thread?

A thread is the smallest unit of execution scheduled by the operating system — a lightweight thread of execution within a process.

All threads in a process share:
- Same address space
- Same heap
- Same global variables

Each thread has:
- Its own stack
- Its own registers / instruction pointer

### Process vs Thread

| Process | Thread |
|------|-------|
| Own address space | Shared address space |
| Heavyweight | Lightweight |
| Slow creation | Fast creation |
| Strong isolation | Requires synchronization |

### What Is Thread Safety?

Thread safety means:
> Correct behavior when multiple threads access shared data concurrently.

Problems arise due to:
- Data races
- Lost updates
- Inconsistent state

### Thread Safety Levels

| Level | Meaning |
|----|-------|
| Not thread-safe | No protection |
| Thread-compatible | Caller synchronizes |
| Thread-safe | Internal synchronization |

---

## std::thread

`std::thread` is the standard C++ threading abstraction introduced in C++11.
It provides a safer, higher-level interface built on top of OS threads. On Linux, `std::thread` internally uses pthread.

Key characteristics:
- C++ abstraction
- RAII-based
- Exception-safe
- Portable across platforms
- Integrates with C++ features

### Ways to Create Threads

A thread starts executing immediately after construction.

#### Using a Free Function

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

#### Using a Lambda

```cpp
std::thread t([]{
    std::cout << "Lambda thread\n";
});
t.join();
```

Most commonly used in modern C++.

#### Using a Functor (Function Object)

```cpp
struct Worker {
    void operator()() {
        std::cout << "Functor thread\n";
    }
};

std::thread t(Worker{});
t.join();
```

#### Using a Member Function

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

### Passing Arguments to Threads

```cpp
void print(int x) {
    std::cout << x << "\n";
}

std::thread t(print, 10);
t.join();
```

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

### Thread Lifecycle: join() vs detach()

- Create → Running → Finished
- Must call `join()` or `detach()`

```cpp
t.join();    // blocks until the thread finishes
t.detach();  // runs independently; no join possible after this
```

Detached threads must not access destroyed objects.

#### Important Destructor Rule

If a `std::thread` object is destroyed while still joinable, `std::terminate()` is called.
This forces developers to explicitly call `join()` or `detach()`.

### Thread Lifetime Issues: Accessing Destroyed Objects

```cpp
void run() {
    int x = 10;
    std::thread t([&]{ std::cout << x; });
    t.detach(); // dangerous
}
```

This leads to undefined behavior.

---

## Synchronization in Standard C++

### Race Conditions (Data Races)

Occurs when:
- Multiple threads access shared data
- At least one write occurs
- No synchronization is used

```cpp
int counter = 0;

void inc() {
    counter++;   // NOT thread-safe
}
```

Multiple threads incrementing `counter` cause undefined behavior.

### std::mutex

A mutex ensures mutual exclusion.

```cpp
#include <mutex>

std::mutex m;
int counter = 0;

void inc() {
    std::lock_guard<std::mutex> lock(m);
    counter++;
}
```

### lock_guard vs unique_lock

| lock_guard | unique_lock |
|----------|------------|
| Simple | Flexible |
| RAII only | Can unlock/lock |
| Faster | Slight overhead |

### Deadlock

Threads wait on each other forever due to circular locking.

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

#### Avoiding Deadlock

- Lock in consistent order
- Use `std::lock`
- Minimize lock scope
- Avoid nested locks

```cpp
std::lock(m1, m2);
std::lock_guard<std::mutex> l1(m1, std::adopt_lock);
std::lock_guard<std::mutex> l2(m2, std::adopt_lock);
```

### Condition Variables

Used for **thread coordination**, not mutual exclusion: a thread waits until a condition becomes true, avoiding busy-waiting.

```cpp
cv.wait(lock, predicate);
cv.notify_one();
cv.notify_all();
```

Always use with a predicate. See the [Producer–Consumer Problem](#producerconsumer-problem) for a full example.

### std::atomic

Used for lock-free operations; faster for simple operations.

```cpp
#include <atomic>

std::atomic<int> counter{0};

void inc() {
    counter++;
}
```

#### atomic vs mutex

| atomic | mutex |
|------|-------|
| Lock-free | Lock-based |
| Fast | Safer for complex logic |
| Limited operations | General purpose |

### Memory Visibility and Ordering

Compilers and CPUs may reorder instructions for optimization.
Synchronization enforces visibility.

```cpp
std::atomic<int> x{0};
x.store(1, std::memory_order_release);
```

### Thread Pool (Concept)

Creating threads repeatedly is expensive. Thread pools reuse threads and improve scalability.

```text
Task Queue -> Worker Threads -> Execute Tasks
```

```cpp
class ThreadPool {
    std::vector<std::thread> workers;
};
```

Used in:
- Servers
- Task schedulers

---

## Classic Multithreading Problems

Interview-famous threading questions:
- Odd–Even printing
- Producer–Consumer
- Thread-safe singleton
- Deadlock scenarios
- Atomic vs mutex

### Odd–Even Printing

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

### Producer–Consumer Problem

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

### Thread-Safe Counter

```cpp
class Counter {
    std::atomic<int> value{0};
public:
    void increment() { value++; }
    int get() const { return value.load(); }
};
```

### Reader–Writer Problem (Simplified)

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

### Dining Philosophers (Simplified)

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

## POSIX Threads (pthreads)

pthread is a low-level threading API defined by the POSIX standard.
It is written in C and directly interfaces with the operating system’s threading model; on Linux, pthread maps closely to kernel threads.

Unlike `std::thread`, pthreads require explicit management of:
- Thread lifecycle
- Synchronization
- Memory visibility
- Error handling

Key characteristics:
- C-based API
- Manual lifecycle management
- Explicit error handling
- POSIX-only (Linux, Unix)
- Maximum control over threading behavior

Understanding pthreads demonstrates strong system-level knowledge.

### pthread_create

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
- Function must return `void*`
- Arguments passed via `void*`

### pthread_join vs pthread_detach

`pthread_join` blocks until the thread exits:

```c
pthread_join(thread, NULL);
```

`pthread_detach` releases thread resources automatically on exit:

```c
pthread_detach(thread);
```

Detached threads must never access objects that may go out of scope.

### Passing Arguments

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

### Race Condition

```c
int counter = 0;

void* increment(void*) {
    counter++;  // race condition
    return NULL;
}
```

### pthread_mutex_t

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

Dynamic initialization:

```c
pthread_mutex_t m;
pthread_mutex_init(&m, NULL);
```

Always destroy the mutex when done:

```c
pthread_mutex_destroy(&m);
```

### Deadlock and trylock

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

Deadlock occurs due to inconsistent lock ordering. Besides consistent lock order and reduced lock scope, pthreads offer `trylock`:

```c
if (pthread_mutex_trylock(&m1) == 0) {
    pthread_mutex_trylock(&m2);
}
```

### Condition Variables: Producer-Consumer

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

### Thread Cancellation

```c
pthread_cancel(thread);
```

Thread must reach a cancellation point.

### Thread-Specific Data (TLS)

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

### Atomic Operations (pthreads + GCC)

Atomic operations prevent race conditions without mutexes.

```c
__sync_fetch_and_add(&counter, 1);
```

Preferred modern approach: C11 atomics.

### Memory Visibility

pthread mutexes and condition variables enforce memory barriers.
Unlocking a mutex guarantees visibility to the next locking thread.

### Advantages and Disadvantages of pthread

Advantages:
- Fine-grained OS control
- Thread attributes (stack size, scheduling policy)
- Thread affinity and priority
- Integration with C libraries
- Suitable for OS-level and infrastructure code

Disadvantages:
- Verbose and error-prone
- No RAII
- Manual resource cleanup
- Harder to maintain
- Easy to introduce subtle bugs

---

## Boost.Thread

Boost.Thread is a threading library from the Boost C++ Libraries.
It predates C++11 and heavily influenced the design of `std::thread`; many Boost.Thread concepts were standardized into C++.

```text
Boost.Thread  -->  C++11 std::thread
```

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

Advantages:
- RAII-based
- Rich feature set (older compilers)
- Familiar API similar to `std::thread`

Disadvantages:
- External dependency
- Not part of C++ standard
- Largely obsolete for new code
- `std::thread` preferred in modern C++

---

## pthread vs std::thread vs Boost.Thread

### Feature Comparison

| Feature | pthread | std::thread | Boost.Thread |
|------|--------|------------|-------------|
| Language | C | C++ | C++ |
| Level | Low-level | High-level | High-level |
| RAII / error handling | No (manual) | Yes | Yes |
| Safety | Programmer-managed | Safer | Safer |
| Portability | POSIX only | Cross-platform | Cross-platform |
| OS Control | Full | Limited | Limited |
| Standard | No | Yes | No |
| Modern Usage | Legacy/System | Preferred | Legacy |

### Advantages and Disadvantages of std::thread

Advantages:
- Automatic resource management (RAII)
- Cleaner syntax
- Cross-platform portability
- Safer default behavior
- Standardized and future-proof

Disadvantages:
- Less direct OS-level control
- No built-in thread affinity API
- Slight abstraction overhead (usually negligible)

### Performance Reality (Linux)

```text
std::thread
  ↓
libstdc++
  ↓
pthread
  ↓
Linux kernel
```

- `std::thread` uses pthread internally
- No meaningful performance penalty
- Safety and portability are gained

### When to Use Which?

Use pthread when:
- You need thread affinity or scheduling control
- You are writing OS-level or infrastructure code
- You integrate with C libraries
- You maintain legacy systems

Use std::thread when:
- Writing modern C++ (C++11+)
- Building application or system-level code
- Portability and safety matter
- Maintainability is important

Use Boost.Thread when:
- Supporting pre-C++11 compilers
- Maintaining legacy Boost-based systems

For new code, Boost.Thread is generally not recommended.

### Trade-off Summary

| Choice | Gain | Cost |
|------|------|-----|
| pthread | Control | Safety, complexity |
| std::thread | Safety, portability | Less OS control |
| Boost.Thread | Compatibility | External dependency |

---

## Performance and Low-Latency Trade-offs

### Performance Considerations

- Threads are expensive; context switches cost CPU cycles — minimize them
- Mutex locking is expensive — avoid locking hot paths
- Avoid fine-grained locking
- Cache contention hurts performance — avoid false sharing
- Prefer stack allocation
- Lock-free is not always faster

### Low-Latency Trade-offs

| Choice | Gain | Cost |
|-----|------|-----|
| Mutex | Safety | Latency |
| Spinlock | Speed | CPU burn |
| Atomic | Fast | Limited |
| Lock-free | Scalability | Complexity |
| TLS | Isolation | Memory |

---

## Best Practices and Common Mistakes

### Best Practices (Golden Rules)

- Minimize shared state
- Prefer immutability
- Always use RAII locks; prefer `lock_guard`
- Keep critical sections small; avoid holding locks long
- Prefer `std::atomic` when possible, but use atomics for simple counters only
- Always join threads; avoid detached threads
- pthreads: always check return codes, use mutex + condition variables together, destroy synchronization objects
- Prefer `std::thread` unless pthread control is required
- Measure before optimizing

### Common Threading Mistakes

- Forgetting join/detach
- Data races
- Deadlocks
- Overusing mutexes
- Using `std::function` in hot paths
- Ignoring false sharing

---

## Common Interview Questions

### What happens if a thread is not joined or detached?

Calling the destructor on a joinable `std::thread` calls `std::terminate`.

### Why does the std::thread destructor call terminate?

To enforce explicit thread lifecycle management and avoid silent bugs.

### Is std::mutex recursive?

No. Use `std::recursive_mutex` if needed.

### Can a mutex be copied?

No. Both `std::mutex` and pthread mutexes are non-copyable.

### What happens if a mutex is not unlocked?

Deadlock or resource starvation.

### Why use condition variables instead of sleep?

sleep wastes CPU and adds latency.

### Is std::thread slower than pthread?

No. On Linux, `std::thread` uses pthread internally.

### Is Boost.Thread the same as std::thread?

Conceptually yes, but `std::thread` is standardized and preferred.

---

## Interview-Grade Explanations

> Multithreading in C++ is built around `std::thread`, synchronization primitives, and strict memory visibility rules.
> Correctness comes first, then performance.
> All threading designs involve trade-offs between safety, latency, and complexity.

> pthreads provide low-level control over thread creation, synchronization, and memory visibility.
> Correct usage requires explicit lifecycle management and strict discipline to avoid race conditions, deadlocks, and undefined behavior.

> “I prefer std::thread for modern C++ because it’s portable, RAII-based, and safer.
> On Linux it uses pthread internally, so there’s no performance loss.
> I use pthread only when I need OS-level control like affinity or when integrating with C libraries.
> Boost.Thread was a precursor to std::thread and is mostly obsolete for new code.”

---

## Final Takeaway

C++ multithreading provides powerful low-level control, but correctness is more important than speed.
It requires disciplined design, proper synchronization, and deep understanding of object lifetimes and memory visibility.

pthread gives control, std::thread gives safety, and Boost.Thread was the bridge between them.
