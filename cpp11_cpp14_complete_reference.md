= C++11 and C++14 — Complete Language Reference
:toc:
:toclevels: 5
:sectnums:
:source-highlighter: coderay

== Introduction

C++11 and C++14 fundamentally modernized C++.

They introduced:
* Deterministic resource management with safer semantics
* True concurrency support
* Move semantics
* Compile-time computation
* Expressive generic programming
* Performance without abstraction cost

This document is a **complete, no-compromise reference**.

---

= PART I — CORE LANGUAGE FUNDAMENTALS

== Compilation Model

C++ is a compiled language with:
* Translation units
* Header inclusion
* Separate compilation
* Link-time optimization

Compilation pipeline:
[source,text]
----
Preprocessing → Compilation → Assembly → Linking
----

---

== Types and Value Categories

=== Fundamental Types

[source,cpp]
----
bool
char / wchar_t / char16_t / char32_t
int / short / long / long long
float / double / long double
void
nullptr_t
----

---

=== Value Categories (CRITICAL)

| Category | Meaning |
|--------|--------|
| lvalue | Has identity |
| xvalue | Expiring value |
| prvalue | Temporary |
| glvalue | lvalue + xvalue |
| rvalue | prvalue + xvalue |

[source,cpp]
----
int x = 10;        // lvalue
int&& r = 10;     // rvalue reference
----

---

== const, constexpr, and literal types

[source,cpp]
----
const int x = runtime();
constexpr int y = 10;
----

C++14 allows constexpr with:
* Loops
* Conditionals
* Multiple statements

---

= PART II — OBJECT LIFETIME & MEMORY MODEL

== Object Lifetime

Phases:
1. Storage allocation
2. Construction
3. Usage
4. Destruction
5. Storage deallocation

---

== Storage Duration

| Type | Example |
|----|-------|
| Automatic | stack |
| Static | global |
| Dynamic | heap |
| Thread-local | TLS |

[source,cpp]
----
thread_local int counter;
----

---

== RAII (FOUNDATIONAL)

[source,cpp]
----
class Lock {
    std::mutex& m;
public:
    Lock(std::mutex& m) : m(m) { m.lock(); }
    ~Lock() { m.unlock(); }
};
----

RAII guarantees:
* Exception safety
* Deterministic cleanup
* No leaks

---

== Rule of 0 / 3 / 5

=== Rule of 0 (preferred)

[source,cpp]
----
class A {
    std::unique_ptr<int> p;
};
----

---

== Copy Semantics

[source,cpp]
----
A(const A&);
A& operator=(const A&);
----

---

== Move Semantics (C++11 CORE FEATURE)

[source,cpp]
----
A(A&&) noexcept;
A& operator=(A&&) noexcept;
----

[source,cpp]
----
std::vector<int> v2 = std::move(v1);
----

---

== std::move vs std::forward

[source,cpp]
----
std::move(x);        // cast to rvalue
std::forward<T>(x);  // perfect forwarding
----

---

= PART III — SMART POINTERS

== unique_ptr

[source,cpp]
----
std::unique_ptr<int> p = std::make_unique<int>(5);
----

* Exclusive ownership
* Zero overhead

---

== shared_ptr

[source,cpp]
----
auto p = std::make_shared<int>(10);
----

* Reference counting
* Atomic overhead

---

== weak_ptr

[source,cpp]
----
std::weak_ptr<int> wp;
----

* Breaks cycles
* Observational access

---

= PART IV — EXPRESSIVE LANGUAGE FEATURES

== auto Type Deduction

[source,cpp]
----
auto x = 10;
auto y = foo();
----

---

== decltype

[source,cpp]
----
decltype(x) y;
----

---

== Range-based for

[source,cpp]
----
for (auto& e : container) {}
----

---

== Uniform Initialization

[source,cpp]
----
int x{10};
std::vector<int> v{1,2,3};
----

---

== nullptr

[source,cpp]
----
int* p = nullptr;
----

---

= PART V — LAMBDAS (C++11 & 14)

== Lambda Basics

[source,cpp]
----
auto f = [](int x){ return x*x; };
----

---

== Capture Modes

[source,cpp]
----
[x]   // by value
[&x]  // by reference
[=]   // all by value
[&]   // all by reference
----

---

== Generic Lambdas (C++14)

[source,cpp]
----
auto add = [](auto a, auto b) {
    return a + b;
};
----

---

= PART VI — TEMPLATES & GENERIC PROGRAMMING

== Function Templates

[source,cpp]
----
template<typename T>
T add(T a, T b);
----

---

== Class Templates

[source,cpp]
----
template<typename T>
class Box { T value; };
----

---

== Variadic Templates

[source,cpp]
----
template<typename... Args>
void log(Args... args);
----

---

== Perfect Forwarding

[source,cpp]
----
template<typename T>
void wrapper(T&& x) {
    f(std::forward<T>(x));
}
----

---

= PART VII — CONCURRENCY (C++11)

== std::thread

[source,cpp]
----
std::thread t(f);
t.join();
----

---

== Mutexes

[source,cpp]
----
std::mutex m;
std::lock_guard<std::mutex> lock(m);
----

---

== Condition Variables

[source,cpp]
----
std::condition_variable cv;
----

---

== Atomics

[source,cpp]
----
std::atomic<int> x{0};
----

Memory ordering:
* relaxed
* acquire
* release
* seq_cst

---

= PART VIII — STL MODERNIZATION

== Containers

* vector
* deque
* list
* map
* unordered_map
* set
* unordered_set
* array
* forward_list

---

== Algorithms

[source,cpp]
----
std::sort
std::find
std::count_if
std::transform
----

---

== Iterator Categories

* input
* output
* forward
* bidirectional
* random-access

---

= PART IX — constexpr (C++14 EXPANDED)

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

= PART X — ERROR HANDLING

== Exceptions

[source,cpp]
----
try {
    throw std::runtime_error("err");
} catch (...) {}
----

---

== noexcept

[source,cpp]
----
void f() noexcept;
----

Used for:
* Optimization
* Move semantics safety

---

= PART XI — LOW-LEVEL & SYSTEM FEATURES

== Alignment

[source,cpp]
----
alignas(64) int x;
----

---

== Type Traits

[source,cpp]
----
std::is_integral<T>::value
----

---

== chrono Library

[source,cpp]
----
using namespace std::chrono;
----

---

= PART XII — C++14 IMPROVEMENTS SUMMARY

C++14 added:
* Generic lambdas
* constexpr expansion
* Return type deduction
* make_unique
* Binary literals
* Digit separators

[source,cpp]
----
int x = 0b1010;
int y = 1'000'000;
----

---

= PART XIII — PERFORMANCE & DESIGN GUIDELINES

== Zero-Cost Abstractions

Abstractions must not cost runtime overhead.

---

== Avoiding Undefined Behavior

* No dangling references
* No data races
* No double delete
* No out-of-bounds access

---

== Best Practices

* Prefer value semantics
* Prefer RAII
* Avoid raw new/delete
* Use standard library
* Measure before optimizing

---

= PART XIV — FINAL SUMMARY

C++11 and C++14 transformed C++ into:
* A safe systems language
* A high-performance concurrency platform
* A modern expressive language

Mastery requires:
* Understanding lifetimes
* Understanding ownership
* Understanding concurrency
* Understanding trade-offs

This document is a **complete foundational reference**.
