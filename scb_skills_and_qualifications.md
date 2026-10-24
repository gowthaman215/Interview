= Technical Skills with Code Examples (C++ / Systems / Python / Database)
:toc:
:toclevels: 3
:sectnums:
:source-highlighter: coderay

== Multithreading

Multithreading allows concurrent execution of multiple threads within a process.

Key goals:
* Improve throughput
* Reduce latency
* Utilize multi-core CPUs

=== Example: Basic Thread Creation

[source,cpp]
----
#include <thread>
#include <iostream>

void worker() {
    std::cout << "Worker thread running\n";
}

int main() {
    std::thread t(worker);
    t.join();
}
----

=== Example: Synchronization Using Mutex

[source,cpp]
----
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
----

---

== C++

C++ is a systems programming language providing low-level control with high-level abstractions.

Key concepts:
* RAII
* Move semantics
* Zero-cost abstractions

=== Example: RAII for Resource Management

[source,cpp]
----
#include <fstream>

void writeFile() {
    std::ofstream file("data.txt");
    file << "Hello";
} // file closed automatically
----

=== Example: Move Semantics

[source,cpp]
----
#include <vector>

std::vector<int> create() {
    std::vector<int> v = {1,2,3};
    return v; // moved, not copied
}
----

---

== Standard Template Library (STL)

STL provides generic containers, algorithms, and iterators.

=== Example: Container + Algorithm

[source,cpp]
----
#include <vector>
#include <algorithm>

int main() {
    std::vector<int> v = {4,1,3};
    std::sort(v.begin(), v.end());
}
----

=== Example: Lambda with Algorithm

[source,cpp]
----
std::count_if(v.begin(), v.end(),
              [](int x){ return x > 2; });
----

---

== Boost Libraries

Boost extends the C++ standard library.

=== Example: Boost.Optional

[source,cpp]
----
#include <boost/optional.hpp>

boost::optional<int> getValue(bool ok) {
    if (ok) return 42;
    return boost::none;
}
----

=== Example: Boost.Asio (Timer)

[source,cpp]
----
#include <boost/asio.hpp>

int main() {
    boost::asio::io_context io;
    boost::asio::steady_timer t(io, std::chrono::seconds(1));
    t.wait();
}
----

---

== Python

Python is used for tooling, automation, and scripting.

=== Example: Simple Automation Script

[source,python]
----
import os

for file in os.listdir("."):
    print(file)
----

=== Example: Simple Function

[source,python]
----
def add(a, b):
    return a + b
----

---

== Database (SQL)

Relational databases store structured data with ACID guarantees.

=== Example: Basic SQL Query

[source,sql]
----
SELECT name, salary
FROM employee
WHERE salary > 50000;
----

=== Example: Simple DDL Operation

[source,sql]
----
CREATE TABLE orders (
    id INT PRIMARY KEY,
    amount DECIMAL(10,2)
);
----

---

== Modern C++14 / C++17 Features

Modern C++ improves safety and expressiveness.

=== Example: Generic Lambda (C++14)

[source,cpp]
----
auto add = [](auto a, auto b) {
    return a + b;
};
----

=== Example: constexpr Function (C++14)

[source,cpp]
----
constexpr int factorial(int n) {
    int r = 1;
    for (int i = 1; i <= n; ++i)
        r *= i;
    return r;
}
----

---

== System, Network, and Low-Latency Programming

Low-latency systems prioritize predictable execution time.

=== Example: Avoid Dynamic Allocation in Hot Path

[source,cpp]
----
int buffer[1024]; // stack allocation
----

=== Example: TCP Socket (POSIX)

[source,cpp]
----
#include <sys/socket.h>
#include <netinet/in.h>
----

(Used for system-level networking.)

---

== Linux Development Environment

Linux provides powerful development tools.

=== Example: Compile and Debug

[source,bash]
----
g++ -g main.cpp -o app
gdb ./app
----

---

== Distributed Systems Architecture

Distributed systems involve multiple cooperating processes.

Key ideas:
* Stateless services
* Horizontal scaling
* Failure handling

=== Example: Simple Producer-Consumer Concept

[source,text]
----
Producer --> Message Queue --> Consumer
----

---

== Messaging and Communication Protocols

Protocols define how data is transmitted.

=== Example: TCP vs UDP Concept

[source,text]
----
TCP: Reliable, ordered
UDP: Fast, best-effort
----

---

== SQL and DDL Operations

DDL defines schema structure.

=== Example: Alter Table

[source,sql]
----
ALTER TABLE orders
ADD COLUMN created_at TIMESTAMP;
----

---

== Communication Skills (Technical Context)

Engineers must communicate:

* Design decisions
* Trade-offs
* Risks
* Implementation details

Clear documentation and discussions reduce system failures.

---

== Final Summary

This document demonstrates:

* Practical multithreading in C++
* Modern C++14/17 usage
* STL and Boost proficiency
* Python scripting support
* Database and SQL fundamentals
* Systems and low-latency awareness

These skills are essential for building **high-performance, scalable, and reliable systems**.
