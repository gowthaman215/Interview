= Modern C++ Utilities (C++11–C++14)
:toc:
:toclevels: 3
:sectnums:
:source-highlighter: coderay

== Introduction

Modern C++ (C++11 and C++14) introduced a rich set of *utilities* that enable:

* Type safety
* Compile-time computation
* Zero-overhead abstractions
* Safer memory management
* Better concurrency support

This document summarizes the **most important utilities** used in real-world systems and interviews.

---

== Container Utilities

=== std::array

Fixed-size container with STL support.

[source,cpp]
----
#include <array>

std::array<int, 3> a = {1, 2, 3};
----

Benefits:
* Stack allocated
* Knows its size
* Works with STL algorithms
* constexpr-friendly

---

=== std::vector

Dynamic array.

[source,cpp]
----
#include <vector>

std::vector<int> v = {1, 2, 3};
----

Use when size is not known at compile time.

---

=== std::pair

Two heterogeneous values.

[source,cpp]
----
std::pair<int, int> p = {10, 20};
----

Used in maps, ranges, return values.

---

=== std::tuple

Multiple heterogeneous values.

[source,cpp]
----
#include <tuple>

auto t = std::make_tuple(1, 3.14, 'a');
----

Often used to return multiple values.

---

== Memory & Ownership Utilities

=== std::unique_ptr

Exclusive ownership smart pointer.

[source,cpp]
----
#include <memory>

auto p = std::make_unique<int>(10);
----

Rules:
* Cannot be copied
* Can be moved
* Zero overhead

---

=== std::shared_ptr

Shared ownership smart pointer.

[source,cpp]
----
auto sp = std::make_shared<int>(20);
----

Reference-counted ownership.

---

=== std::weak_ptr

Non-owning reference to shared_ptr.

[source,cpp]
----
std::weak_ptr<int> wp;
----

Prevents circular references.

---

== Functional Utilities

=== std::function

Type-erased callable wrapper.

[source,cpp]
----
#include <functional>

std::function<int(int,int)> add =
    [](int a, int b){ return a + b; };
----

Used for callbacks and APIs.

---

=== std::bind

Argument binding utility.

[source,cpp]
----
auto add5 = std::bind(add, 5, std::placeholders::_1);
----

Note: Lambdas are preferred.

---

== Type System & Metaprogramming

=== decltype

Deduces type of an expression.

[source,cpp]
----
int x = 10;
decltype(x) y = 20;
----

---

=== auto

Type inference.

[source,cpp]
----
auto x = 10;
auto y = 3.14;
----

Improves readability and reduces verbosity.

---

=== <type_traits>

Compile-time type inspection.

[source,cpp]
----
#include <type_traits>

static_assert(std::is_integral<int>::value, "Not integral");
----

Used heavily in templates.

---

=== std::integer_sequence (C++14)

Compile-time index generation.

[source,cpp]
----
std::index_sequence<0,1,2>
----

Used in tuple unpacking.

---

== Compile-Time Utilities

=== constexpr

Allows compile-time evaluation.

[source,cpp]
----
constexpr int square(int x) {
    return x * x;
}
----

C++14 allows loops and conditionals.

---

=== static_assert

Compile-time assertion.

[source,cpp]
----
static_assert(sizeof(int) == 4, "Unexpected int size");
----

---

=== std::numeric_limits

Type limits and properties.

[source,cpp]
----
#include <limits>

int maxInt = std::numeric_limits<int>::max();
----

Used for overflow protection and numeric safety.

---

== Time Utilities

=== std::chrono

Type-safe time handling.

[source,cpp]
----
#include <chrono>

using namespace std::chrono;
seconds s = 5s;
milliseconds ms = 500ms;
----

---

=== Measuring execution time

[source,cpp]
----
auto start = std::chrono::high_resolution_clock::now();
// work
auto end = std::chrono::high_resolution_clock::now();
----

---

== Concurrency Utilities

=== std::thread

Create threads.

[source,cpp]
----
#include <thread>

std::thread t([]{
    // work
});
t.join();
----

---

=== std::mutex and std::lock_guard

Synchronization primitives.

[source,cpp]
----
#include <mutex>

std::mutex m;
std::lock_guard<std::mutex> lock(m);
----

RAII-based locking.

---

== Random Utilities

=== <random>

Modern random number generation.

[source,cpp]
----
#include <random>

std::mt19937 gen;
std::uniform_int_distribution<> dist(1, 6);
----

Avoids problems of rand().

---

== Move Semantics Utilities

=== std::move

Enables move semantics.

[source,cpp]
----
std::vector<int> v2 = std::move(v1);
----

Transfers ownership.

---

=== std::forward

Perfect forwarding.

[source,cpp]
----
template<typename T>
void f(T&& t) {
    g(std::forward<T>(t));
}
----

Used in templates.

---

== Initialization Utilities

=== std::initializer_list

Uniform initialization.

[source,cpp]
----
std::vector<int> v = {1, 2, 3};
----

---

== Exception & Optimization Utilities

=== noexcept

Indicates function does not throw.

[source,cpp]
----
void foo() noexcept;
----

Improves optimization and correctness.

---

== Most Important Utilities (Interview Focus)

* std::vector
* std::array
* std::unique_ptr
* std::shared_ptr
* std::function
* std::chrono
* constexpr
* static_assert
* std::thread
* std::move

---

== Final Takeaway

Modern C++ utilities allow you to write:

* Faster code
* Safer code
* Cleaner abstractions

All **without runtime overhead**.

Use them correctly — and C++ becomes a powerful, expressive language.
