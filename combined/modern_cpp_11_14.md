# Modern C++ (C++11 / C++14)

A single, de-duplicated reference merged from:
`cpp11_cpp14_simple_complete_reference.md`, `cpp11_cpp14_complete_reference.md`,
`cpp14_new_features_and_constexpr.md` and `modern_cpp_utilities.md`.

## Contents

1. [Introduction](#introduction)
2. [Core Language Fundamentals](#core-language-fundamentals)
3. [Object Lifetime and Memory](#object-lifetime-and-memory)
4. [Smart Pointers](#smart-pointers)
5. [Expressive Language Features](#expressive-language-features)
6. [Lambdas and Functional Utilities](#lambdas-and-functional-utilities)
7. [Templates and Generic Programming](#templates-and-generic-programming)
8. [Compile-Time Programming](#compile-time-programming)
9. [Concurrency (C++11)](#concurrency-c11)
10. [STL Containers, Algorithms and Iterators](#stl-containers-algorithms-and-iterators)
11. [Error Handling](#error-handling)
12. [Low-Level, Time and Random Utilities](#low-level-time-and-random-utilities)
13. [What C++14 Added](#what-c14-added)
14. [Performance, Best Practices and Undefined Behavior](#performance-best-practices-and-undefined-behavior)
15. [Most Important Utilities (Interview Focus)](#most-important-utilities-interview-focus)
16. [Final Takeaway](#final-takeaway)

---

## Introduction

C++11 and C++14 fundamentally modernized C++. They introduced:

- Deterministic, safer resource and memory management
- Move semantics
- True concurrency support
- Compile-time computation
- Better templates and expressive generic programming
- Expressive syntax
- Zero-cost abstractions (performance without abstraction cost)

C++14 is a **refinement release** over C++11. It does not introduce radical new concepts, but it:

- Simplifies C++11 features
- Reduces boilerplate
- Improves compile-time programming
- Makes modern C++ more practical in real projects

---

## Core Language Fundamentals

### Program Structure

```cpp
#include <iostream>

int main() {
    std::cout << "Hello";
}
```

Execution starts at `main()`.

### Compilation Model

C++ is a compiled language with:
- Translation units
- Header inclusion
- Separate compilation
- Link-time optimization

Compilation pipeline:
```text
Preprocessing → Compilation → Assembly → Linking
```

### Fundamental Types and Variables

```cpp
bool
char / wchar_t / char16_t / char32_t
int / short / long / long long
float / double / long double
void
nullptr_t
```

```cpp
int a = 10;
double b = 3.14;
bool ok = true;
```

C++ is statically typed.

### Value Categories (Critical)

| Category | Meaning |
|--------|--------|
| lvalue | Has identity |
| xvalue | Expiring value |
| prvalue | Pure temporary |
| glvalue | lvalue + xvalue |
| rvalue | prvalue + xvalue |

```cpp
int x = 10;        // lvalue
int&& r = 10;      // rvalue reference
```

### Pointers, References and nullptr

```cpp
int x = 10;
int* p = &x;
int& r = x;
int* q = nullptr;
```

- Pointer → address
- Reference → alias
- `nullptr` → type-safe null pointer

### const vs constexpr

```cpp
const int x = runtime();
constexpr int y = 10;
```

- `const` → runtime constant
- `constexpr` → compile-time constant

See [Compile-Time Programming](#compile-time-programming) for what C++14 allows inside `constexpr`.

---

## Object Lifetime and Memory

### Object Lifetime

Phases:
1. Storage allocation
2. Construction
3. Usage
4. Destruction
5. Storage deallocation

### Storage Duration

| Type | Example |
|----|-------|
| Automatic | stack |
| Static | global |
| Dynamic | heap |
| Thread-local | TLS |

```cpp
thread_local int counter;
```

### Stack vs Heap

```cpp
int a = 10;            // stack
int* p = new int(5);   // heap
delete p;
```

Prefer stack and RAII.

### Constructors and Destructors

```cpp
class A {
public:
    A() {}
    ~A() {}
};
```

Destructors are called automatically.

### RAII (Foundational)

```cpp
#include <fstream>

void write() {
    std::ofstream f("data.txt");
    f << "Hello";
} // file closed automatically
```

```cpp
class Lock {
    std::mutex& m;
public:
    Lock(std::mutex& m) : m(m) { m.lock(); }
    ~Lock() { m.unlock(); }
};
```

RAII guarantees:
- Exception safety
- Deterministic cleanup
- No leaks

### Rule of 0 / 3 / 5

- Rule of 0 (preferred) → Use RAII types, write no special members
- Rule of 3 → Destructor, copy ctor, copy assign
- Rule of 5 → + move ctor, move assign

```cpp
class Safe {
    std::unique_ptr<int> p;   // Rule of 0
};
```

### Copy Semantics

```cpp
A(const A&);
A& operator=(const A&);
```

### Move Semantics (C++11 Core Feature)

```cpp
A(A&&) noexcept;
A& operator=(A&&) noexcept;
```

```cpp
std::vector<int> v1 = {1,2};
std::vector<int> v2 = std::move(v1);
```

Moves (transfers) ownership instead of copying.

### std::move vs std::forward

```cpp
std::move(x);        // cast to rvalue
std::forward<T>(x);  // preserve value category (perfect forwarding)
```

Perfect forwarding in templates:

```cpp
template<typename T>
void wrapper(T&& x) {
    f(std::forward<T>(x));
}
```

---

## Smart Pointers

### unique_ptr

Exclusive ownership smart pointer.

```cpp
#include <memory>

auto p = std::make_unique<int>(10);   // C++14
```

- Exclusive ownership
- Cannot be copied
- Can be moved
- Zero overhead

`std::make_unique` (new in C++14):
- Prevents memory leaks
- Exception-safe
- Encourages unique ownership

### shared_ptr

Shared, reference-counted ownership.

```cpp
auto sp = std::make_shared<int>(20);
```

- Reference counting
- Atomic overhead

### weak_ptr

Non-owning reference to a `shared_ptr`.

```cpp
std::weak_ptr<int> wp;
```

- Breaks (prevents) circular references
- Observational access

---

## Expressive Language Features

### auto

Type inference.

```cpp
auto x = 10;
auto y = 3.14;
auto z = foo();
```

Improves readability and reduces verbosity.

### decltype

Deduces the type of an expression.

```cpp
int x = 10;
decltype(x) y = 20;
```

### Auto Return Type Deduction (C++14)

The compiler deduces function return types automatically.

```cpp
auto add(int a, int b) {
    return a + b;
}
```

Benefits:
- Less verbose
- Cleaner APIs
- Easier refactoring

### Range-based for

```cpp
for (auto& e : container) {}
```

### Uniform Initialization and std::initializer_list

```cpp
int x{10};
std::vector<int> v{1,2,3};
std::vector<int> w = {1, 2, 3};   // std::initializer_list
```

Brace initialization prevents narrowing.

---

## Lambdas and Functional Utilities

### Lambda Basics

```cpp
auto f = [](int x) { return x*x; };
```

### Capture Modes

```cpp
[x]   // by value
[&x]  // by reference
[=]   // all by value
[&]   // all by reference
```

### Generic Lambdas (C++14)

C++14 allows `auto` in lambda parameters.

```cpp
auto add = [](auto a, auto b) {
    return a + b;
};
```

Benefits:
- Type-independent lambdas
- Reusable functional code
- Simplifies templates

### Lambda Return Type Deduction (C++14)

```cpp
auto absVal = [](int x) {
    if (x < 0) return -x;
    return x;
};
```

### std::function

Type-erased callable wrapper, used for callbacks and APIs.

```cpp
#include <functional>

std::function<int(int,int)> add =
    [](int a, int b){ return a + b; };
```

### std::bind

Argument binding utility.

```cpp
auto add5 = std::bind(add, 5, std::placeholders::_1);
```

Note: Lambdas are preferred.

---

## Templates and Generic Programming

### Function Templates

```cpp
template<typename T>
T add(T a, T b);
```

### Class Templates

```cpp
template<typename T>
class Box { T value; };
```

### Variadic Templates

```cpp
template<typename... Args>
void log(Args... args);
```

### Variable Templates (C++14)

Templates for variables.

```cpp
template<typename T>
constexpr T pi = T(3.1415926535897932385L);
```

### Perfect Forwarding

See [std::move vs std::forward](#stdmove-vs-stdforward).

---

## Compile-Time Programming

### constexpr

Allows compile-time evaluation.

```cpp
constexpr int square(int x) {
    return x * x;
}
```

C++14 removes many C++11 restrictions. Now allowed:
- Local variables
- Loops
- Conditionals
- Multiple statements

```cpp
constexpr int factorial(int n) {
    int result = 1;
    for (int i = 1; i <= n; ++i)
        result *= i;
    return result;
}
```

### static_assert

Compile-time assertion.

```cpp
static_assert(sizeof(int) == 4, "Unexpected int size");
```

### Type Traits (`<type_traits>`)

Compile-time type inspection, used heavily in templates.

```cpp
#include <type_traits>

static_assert(std::is_integral<int>::value, "Not integral");
```

### std::integer_sequence (C++14)

Compile-time index generation, used in tuple unpacking.

```cpp
std::index_sequence<0,1,2>
```

### std::numeric_limits

Type limits and properties, used for overflow protection and numeric safety.

```cpp
#include <limits>

int maxInt = std::numeric_limits<int>::max();
```

---

## Concurrency (C++11)

### std::thread

```cpp
#include <thread>

std::thread t([]{
    // work
});
t.join();
```

### std::mutex and std::lock_guard

RAII-based locking.

```cpp
#include <mutex>

std::mutex m;
std::lock_guard<std::mutex> lock(m);
```

### Condition Variables

```cpp
std::condition_variable cv;
```

### Atomics

```cpp
std::atomic<int> x{0};
x++;
```

Memory ordering:
- relaxed
- acquire
- release
- seq_cst

### Memory Model (Simple View)

- Data race → undefined behavior
- Mutex / atomic → synchronization
- Happens-before rules

---

## STL Containers, Algorithms and Iterators

### Containers

- vector
- deque
- list
- map
- unordered_map
- set
- unordered_set
- array
- forward_list

```cpp
std::vector<int> v;
std::unordered_map<int,int> m;
```

#### std::array

Fixed-size container with STL support.

```cpp
#include <array>

std::array<int, 3> a = {1, 2, 3};
```

- Stack allocated
- Knows its size
- Works with STL algorithms
- constexpr-friendly

#### std::vector

Dynamic array; use when size is not known at compile time.

```cpp
#include <vector>

std::vector<int> v = {1, 2, 3};
```

#### std::pair and std::tuple

`pair` holds two heterogeneous values (used in maps, ranges, return values); `tuple` holds several and is often used to return multiple values.

```cpp
#include <tuple>

std::pair<int, int> p = {10, 20};
auto t = std::make_tuple(1, 3.14, 'a');
```

### Algorithms

```cpp
std::sort(v.begin(), v.end());
std::find(v.begin(), v.end(), 5);
std::count_if(...);
std::transform(...);
```

Prefer algorithms over loops.

### Iterators

```cpp
for (auto it = v.begin(); it != v.end(); ++it) {}
```

Iterator categories:
- input
- output
- forward
- bidirectional
- random-access

---

## Error Handling

### Exceptions

```cpp
try {
    throw std::runtime_error("err");
} catch (...) {}
```

### noexcept

Indicates a function does not throw.

```cpp
void f() noexcept;
```

Used for:
- Optimization
- Move semantics safety (required for safe move operations)

---

## Low-Level, Time and Random Utilities

### Alignment

```cpp
alignas(64) int x;
```

### std::chrono

Type-safe time handling.

```cpp
#include <chrono>

using namespace std::chrono;
seconds s = 5s;
milliseconds ms = 500ms;
auto t = steady_clock::now();
```

Measuring execution time:

```cpp
auto start = std::chrono::high_resolution_clock::now();
// work
auto end = std::chrono::high_resolution_clock::now();
```

### `<random>`

Modern random number generation; avoids the problems of `rand()`.

```cpp
#include <random>

std::mt19937 gen;
std::uniform_int_distribution<> dist(1, 6);
```

---

## What C++14 Added

- Generic lambdas
- Lambda return type deduction
- Function return type deduction
- constexpr expansion
- `std::make_unique`
- Variable templates
- `std::integer_sequence`
- Binary literals
- Digit separators

```cpp
int x = 0b1010;
int y = 1'000'000;
```

---

## Performance, Best Practices and Undefined Behavior

### Zero-Cost Abstractions

Abstractions must not cost runtime overhead.

### Performance Principles

- Prefer stack
- Avoid heap in hot paths
- Avoid virtual calls in critical loops
- Use cache-friendly data
- Measure before optimizing

### Best Practices

- Prefer value semantics
- Prefer RAII
- Avoid raw new/delete
- Use the standard library

### Common Undefined Behavior

- Dangling reference
- Double delete
- Data race
- Out-of-bounds access
- Using moved-from object

---

## Most Important Utilities (Interview Focus)

- std::vector
- std::array
- std::unique_ptr
- std::shared_ptr
- std::function
- std::chrono
- constexpr
- static_assert
- std::thread
- std::move

---

## Final Takeaway

C++11 and C++14 transformed C++ into:
- A safe systems language
- A high-performance concurrency platform
- A modern expressive language

Modern C++ utilities let you write faster, safer code with cleaner abstractions — all **without runtime overhead**.

Mastery requires understanding:
- Ownership
- Lifetime
- Concurrency
- Trade-offs
