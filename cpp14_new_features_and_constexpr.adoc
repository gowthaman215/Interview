= C++14 New Features and constexpr Explained
:toc:
:toclevels: 3
:sectnums:
:source-highlighter: coderay

== Introduction

C++14 is a *refinement release* over C++11.
It does not introduce radical new concepts, but it:

* Simplifies C++11 features
* Reduces boilerplate
* Improves compile-time programming
* Makes modern C++ more practical in real projects

This document explains:
* What is new in C++14
* Why `constexpr` is important and widely used

---

== What Is New in C++14

=== 1. Auto Return Type Deduction for Functions

C++14 allows the compiler to deduce function return types automatically.

[source,cpp]
----
auto add(int a, int b) {
    return a + b;
}
----

Benefits:
* Less verbose
* Cleaner APIs
* Easier refactoring

---

=== 2. Generic Lambdas

C++14 allows `auto` in lambda parameters.

[source,cpp]
----
auto sum = [](auto a, auto b) {
    return a + b;
};
----

Benefits:
* Type-independent lambdas
* Reusable functional code
* Simplifies templates

---

=== 3. Lambda Return Type Deduction

Return type is deduced automatically.

[source,cpp]
----
auto absVal = [](int x) {
    if (x < 0) return -x;
    return x;
};
----

---

=== 4. More Powerful constexpr (Major Improvement)

C++14 removes many restrictions from C++11 constexpr.

Now allowed:
* Local variables
* Loops
* Conditionals

[source,cpp]
----
constexpr int factorial(int n) {
    int result = 1;
    for (int i = 1; i <= n; ++i)
        result *= i;
    return result;
}
----

---

=== 5. std::make_unique (New Utility)

C++14 introduces `std::make_unique`.

[source,cpp]
----
#include <memory>

auto p = std::make_unique<int>(10);
----

Benefits:
* Prevents memory leaks
* Exception-safe
* Encourages unique ownership

---

=== 6. Variable Templates

Templates for variables.

[source,cpp]
----
template<typename T>
constexpr T pi = T(
