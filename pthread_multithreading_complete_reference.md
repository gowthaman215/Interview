= POSIX Threads (pthreads) – Complete Multithreading Reference with Examples
:toc:
:toclevels: 4
:sectnums:
:source-highlighter: coderay

== Introduction

POSIX Threads (pthreads) is a low-level threading API provided by POSIX-compliant operating systems.
It is widely used in system programming, high-performance systems, and legacy C/C++ codebases.

Unlike std::thread, pthreads require explicit management of:
* Thread lifecycle
* Synchronization
* Memory visibility
* Error handling

Understanding pthreads demonstrates strong system-level knowledge.

---

== Thread Basics

=== What Is a pthread?

A pthread represents a lightweight thread of execution within a process.
All threads share:
* Address space
* Heap
* Global variables

Each thread has:
* Its own stack
* Its own registers

---

== Creating Threads

=== pthread_create

[source,c]
----
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
----

Key points:
* Thread starts immediately after creation
* Function must return void*
* Arguments passed via void*

---

== Joining vs Detaching Threads

=== pthread_join

Blocks until the thread exits.

[source,c]
----
pthread_join(thread, NULL);
----

---

=== pthread_detach

Thread resources released automatically on exit.

[source,c]
----
pthread_detach(thread);
----

Detached threads must never access objects that may go out of scope.

---

== Passing Arguments to Threads

[source,c]
----
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
----

Caller must ensure argument lifetime.

---

== Race Conditions

=== Unsafe Shared Access

[source,c]
----
int counter = 0;

void* increment(void*) {
    counter++;  // race condition
    return NULL;
}
----

Multiple threads updating shared data without synchronization leads to undefined behavior.

---

== Mutex

=== pthread_mutex_t

Mutex provides mutual exclusion.

[source,c]
----
#include <pthread.h>

pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;
int counter = 0;

void* increment(void*) {
    pthread_mutex_lock(&mutex);
    counter++;
    pthread_mutex_unlock(&mutex);
    return NULL;
}
----

---

=== Mutex Initialization

[source,c]
----
pthread_mutex_t m;
pthread_mutex_init(&m, NULL);
----

Always destroy mutex when done:

[source,c]
----
pthread_mutex_destroy(&m);
----

---

== Deadlock

=== Deadlock Example

[source,c]
----
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
----

Deadlock occurs due to inconsistent lock ordering.

---

=== Avoiding Deadlock

* Enforce consistent lock order
* Use trylock
* Reduce lock scope

[source,c]
----
if (pthread_mutex_trylock(&m1) == 0) {
    pthread_mutex_trylock(&m2);
}
----

---

== Condition Variables

=== Why Condition Variables?

Used to block threads until a condition becomes true.
Avoids busy-waiting.

---

=== Producer-Consumer Example

[source,c]
----
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
----

---

== Thread Cancellation

=== pthread_cancel

[source,c]
----
pthread_cancel(thread);
----

Thread must reach a cancellation point.

---

== Thread-Specific Data (TLS)

=== Thread-Local Storage

[source,c]
----
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
----

Each thread gets its own data copy.

---

== Atomic Operations (pthreads + GCC)

Atomic operations prevent race conditions without mutexes.

[source,c]
----
__sync_fetch_and_add(&counter, 1);
----

Preferred modern approach: C11 atomics.

---

== Memory Visibility

pthread mutex and condition variables enforce memory barriers.
Unlocking a mutex guarantees visibility to the next locking thread.

---

== Performance Considerations

* Mutex locking is expensive
* Context switches cost CPU cycles
* Avoid fine-grained locking
* Avoid false sharing

---

== pthread vs std::thread

| Feature | pthread | std::thread |
|------|--------|------------|
| Abstraction | Low-level | High-level |
| Portability | POSIX only | Cross-platform |
| Error handling | Manual | RAII |
| Safety | Programmer-managed | Safer |

pthreads offer more control but require discipline.

---

== Low-Latency Trade-offs

| Choice | Gain | Cost |
|-----|------|-----|
| Mutex | Safety | Latency |
| Spinlock | Speed | CPU burn |
| Lock-free | Scalability | Complexity |
| TLS | Isolation | Memory |

---

== Common Interview Questions

=== What happens if mutex is not unlocked?

Deadlock or resource starvation.

---

=== Can mutex be copied?

No. pthread mutexes are non-copyable.

---

=== Why use condition variables instead of sleep?

sleep wastes CPU and adds latency.

---

== Best Practices

* Always check return codes
* Use mutex + condition variables together
* Avoid detached threads
* Minimize shared state
* Destroy synchronization objects
* Prefer std::thread unless pthread control is required

---

== Interview-Grade Explanation

> pthreads provide low-level control over thread creation, synchronization, and memory visibility.  
> Correct usage requires explicit lifecycle management and strict discipline to avoid race conditions, deadlocks, and undefined behavior.

---

== One-Line Takeaway

POSIX Threads offer powerful, low-level concurrency control but demand precise design, careful synchronization, and deep understanding of thread lifetimes.

