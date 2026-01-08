= pthread vs std::thread vs Boost.Thread — Complete Reference
:toc:
:toclevels: 4
:sectnums:
:source-highlighter: coderay

== Introduction

C++ supports multithreading through multiple libraries and abstractions.
The most commonly encountered are:

* POSIX Threads (pthread)
* C++ Standard Library threads (std::thread)
* Boost.Thread

Understanding the differences, trade-offs, and use cases of each is critical for
system programming, low-latency systems, and technical interviews.

---

== pthread (POSIX Threads)

=== What is pthread?

pthread is a low-level threading API defined by the POSIX standard.
It is written in C and directly interfaces with the operating system’s threading model.

On Linux, pthread maps closely to kernel threads.

---

=== Key Characteristics

* C-based API
* Manual lifecycle management
* Explicit error handling
* POSIX-only (Linux, Unix)
* Maximum control over threading behavior

---

=== Basic Example

[source,c]
----
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
----

---

=== Advantages of pthread

* Fine-grained OS control
* Thread attributes (stack size, scheduling policy)
* Thread affinity and priority
* Integration with C libraries
* Suitable for OS-level and infrastructure code

---

=== Disadvantages of pthread

* Verbose and error-prone
* No RAII
* Manual resource cleanup
* Harder to maintain
* Easy to introduce subtle bugs

---

== std::thread (C++ Standard Thread)

=== What is std::thread?

`std::thread` is the standard C++ threading abstraction introduced in C++11.
It provides a safer, higher-level interface built on top of OS threads.

On Linux, `std::thread` internally uses pthread.

---

=== Key Characteristics

* C++ abstraction
* RAII-based
* Exception-safe
* Portable across platforms
* Integrates with C++ features

---

=== Basic Example

[source,cpp]
----
#include <thread>
#include <iostream>

void worker() {
    std::cout << "std::thread running\n";
}

int main() {
    std::thread t(worker);
    t.join();
}
----

---

=== Advantages of std::thread

* Automatic resource management (RAII)
* Cleaner syntax
* Cross-platform portability
* Safer default behavior
* Standardized and future-proof

---

=== Disadvantages of std::thread

* Less direct OS-level control
* No built-in thread affinity API
* Slight abstraction overhead (usually negligible)

---

=== Important Destructor Rule

If a `std::thread` object is destroyed while still joinable:

[source,cpp]
----
std::terminate();
----

This forces developers to explicitly call `join()` or `detach()`.

---

== Boost.Thread

=== What is Boost.Thread?

Boost.Thread is a threading library from the Boost C++ Libraries.
It predates C++11 and heavily influenced the design of `std::thread`.

---

=== Historical Context

Timeline:

[source,text]
----
Boost.Thread  -->  C++11 std::thread
----

Many Boost.Thread concepts were standardized into C++.

---

=== Basic Example

[source,cpp]
----
#include <boost/thread.hpp>
#include <iostream>

void worker() {
    std::cout << "boost::thread running\n";
}

int main() {
    boost::thread t(worker);
    t.join();
}
----

---

=== Advantages of Boost.Thread

* RAII-based
* Rich feature set (older compilers)
* Familiar API similar to std::thread

---

=== Disadvantages of Boost.Thread

* External dependency
* Not part of C++ standard
* Largely obsolete for new code
* std::thread preferred in modern C++

---

== Feature Comparison

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

== Performance Reality (Linux)

On Linux systems:

[source,text]
----
std::thread
  ↓
libstdc++
  ↓
pthread
  ↓
Linux kernel
----

Important points:
* std::thread uses pthread internally
* No meaningful performance penalty
* Safety and portability are gained

---

== When to Use Which?

=== Use pthread when:

* You need thread affinity or scheduling control
* You are writing OS-level or infrastructure code
* You integrate with C libraries
* You maintain legacy systems

---

=== Use std::thread when:

* Writing modern C++ (C++11+)
* Building application or system-level code
* Portability and safety matter
* Maintainability is important

---

=== Use Boost.Thread when:

* Supporting pre-C++11 compilers
* Maintaining legacy Boost-based systems

For new code, Boost.Thread is generally not recommended.

---

== Interview-Grade Explanation

A strong interview answer:

> “I prefer std::thread for modern C++ because it’s portable, RAII-based, and safer.  
> On Linux it uses pthread internally, so there’s no performance loss.  
> I use pthread only when I need OS-level control like affinity or when integrating with C libraries.  
> Boost.Thread was a precursor to std::thread and is mostly obsolete for new code.”

---

== Common Interview Questions

=== Is std::thread slower than pthread?

No. On Linux, std::thread uses pthread internally.

---

=== Is Boost.Thread the same as std::thread?

Conceptually yes, but std::thread is standardized and preferred.

---

=== Why does std::thread destructor call terminate?

To enforce explicit thread lifecycle management and avoid silent bugs.

---

== Trade-off Summary

| Choice | Gain | Cost |
|------|------|-----|
| pthread | Control | Safety, complexity |
| std::thread | Safety, portability | Less OS control |
| Boost.Thread | Compatibility | External dependency |

---

== One-Line Takeaway

pthread gives control, std::thread gives safety, and Boost.Thread was the bridge between them.

