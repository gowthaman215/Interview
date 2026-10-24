# Modern C++ (C++11 / C++14)

This file combines the following references into one document:

1. [C++11 and C++14 — Simple Complete Reference](#part-1) — from `cpp11_cpp14_simple_complete_reference.md`
2. [C++11 and C++14 — Complete Language Reference](#part-2) — from `cpp11_cpp14_complete_reference.md`
3. [C++14 New Features and constexpr Explained](#part-3) — from `cpp14_new_features_and_constexpr.md`
4. [Modern C++ Utilities (C++11–C++14)](#part-4) — from `modern_cpp_utilities.md`

---

<a id="part-1"></a>

*Part 1 — source: `cpp11_cpp14_simple_complete_reference.md`*

## C++11 and C++14 — Simple Complete Reference

### 1. Introduction

C++11 and C++14 modernized C++ completely.

They introduced:
- Safer memory management
- Move semantics
- Concurrency support
- Better templates
- Expressive syntax
- Zero-cost abstractions

This document is a **simple but complete reference**.

---

### 2. Basic Language Elements

#### Program Structure

```cpp
#include <iostream>

int main() {
    std::cout << "Hello";
}
```

Execution starts at `main()`.

---

#### Variables and Types

```cpp
int a = 10;
double b = 3.14;
bool ok = true;
```

C++ is statically typed.

---

#### const vs constexpr

```cpp
const int x = runtime();
constexpr int y = 10;
```

- `const` → runtime constant
- `constexpr` → compile-time constant

C++14 allows loops inside `constexpr`.

---

### 3. Value Categories (Very Important)

| Category | Meaning |
|--------|--------|
| lvalue | Has identity |
| rvalue | Temporary |
| xvalue | Expiring |
| prvalue | Pure temporary |

```cpp
int x = 10;
int&& r = 20;
```

---

### 4. Pointers and References

```cpp
int x = 10;
int* p = &x;
int& r = x;
```

- Pointer → address
- Reference → alias

---

### 5. Memory and Lifetime

#### Stack vs Heap

```cpp
int a = 10;
int* p = new int(5);
delete p;
```

Prefer stack and RAII.

---

### 6. RAII (FOUNDATION)

```cpp
#include <fstream>

void write() {
    std::ofstream f("data.txt");
    f << "Hello";
} // file closed automatically
```

RAII guarantees cleanup.

---

### 7. Object Lifecycle

#### Constructors and Destructors

```cpp
class A {
public:
    A() {}
    ~A() {}
};
```

Destructors are called automatically.

---

### 8. Rule of 0 / 3 / 5

- Rule of 0 → Use RAII types
- Rule of 3 → Destructor, copy ctor, copy assign
- Rule of 5 → + move ctor, move assign

```cpp
class Safe {
    std::unique_ptr<int> p;
};
```

---

### 9. Move Semantics (C++11 Core)

```cpp
std::vector<int> v1 = {1,2};
std::vector<int> v2 = std::move(v1);
```

Moves ownership instead of copying.

---

### 10. std::move vs std::forward

```cpp
std::move(x);        // cast to rvalue
std::forward<T>(x);  // preserve value category
```

---

### 11. Smart Pointers

#### unique_ptr

```cpp
auto p = std::make_unique<int>(10);
```

Exclusive ownership.

---

#### shared_ptr

```cpp
auto p = std::make_shared<int>(10);
```

Reference-counted ownership.

---

#### weak_ptr

```cpp
std::weak_ptr<int> wp;
```

Breaks cycles.

---

### 12. auto and decltype

```cpp
auto x = 10;
decltype(x) y = 20;
```

---

### 13. Uniform Initialization

```cpp
int x{10};
std::vector<int> v{1,2,3};
```

Prevents narrowing.

---

### 14. Lambdas (C++11 / C++14)

#### Basic Lambda

```cpp
auto f = [](int x) { return x*x; };
```

---

#### Capture

```cpp
[x]  // by value
[&x] // by reference
```

---

#### Generic Lambda (C++14)

```cpp
auto add = [](auto a, auto b) {
    return a + b;
};
```

---

### 15. Templates

#### Function Template

```cpp
template<typename T>
T add(T a, T b);
```

---

#### Class Template

```cpp
template<typename T>
class Box { T value; };
```

---

#### Variadic Templates

```cpp
template<typename... Args>
void log(Args... args);
```

---

### 16. STL Containers

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

---

### 17. STL Algorithms

```cpp
std::sort(v.begin(), v.end());
std::find(v.begin(), v.end(), 5);
```

Prefer algorithms over loops.

---

### 18. Iterators

```cpp
for (auto it = v.begin(); it != v.end(); ++it) {}
```

---

### 19. Multithreading (C++11)

#### std::thread

```cpp
std::thread t(f);
t.join();
```

---

#### mutex

```cpp
std::mutex m;
std::lock_guard<std::mutex> lock(m);
```

---

#### condition_variable

```cpp
std::condition_variable cv;
```

---

#### atomic

```cpp
std::atomic<int> x{0};
x++;
```

---

### 20. Memory Model (Simple View)

- Data race → undefined behavior
- Mutex / atomic → synchronization
- Happens-before rules

---

### 21. chrono (Time)

```cpp
using namespace std::chrono;
auto t = steady_clock::now();
```

---

### 22. constexpr (C++14 Expanded)

```cpp
constexpr int fact(int n) {
    int r = 1;
    for(int i=1;i<=n;i++) r*=i;
    return r;
}
```

---

### 23. noexcept

```cpp
void f() noexcept;
```

Required for safe move operations.

---

### 24. Type Traits

```cpp
std::is_integral<int>::value
```

Used in templates.

---

### 25. Low-Level Features

#### Alignment

```cpp
alignas(64) int x;
```

---

### 26. Error Handling

```cpp
try {
    throw std::runtime_error("err");
} catch(...) {}
```

---

### 27. C++14 Additions Summary

- Generic lambdas
- make_unique
- constexpr expansion
- Return type deduction
- Binary literals
- Digit separators

```cpp
int x = 0b1010;
int y = 1'000'000;
```

---

### 28. Performance Principles

- Prefer stack
- Avoid heap in hot paths
- Avoid virtual calls in critical loops
- Use cache-friendly data
- Measure before optimizing

---

### 29. Common Undefined Behavior

- Dangling reference
- Double delete
- Data race
- Out-of-bounds access
- Using moved-from object

---

### 30. Final Takeaway

C++11 and C++14 transformed C++ into:
- A safe systems language
- A high-performance concurrency platform
- A modern expressive language

Mastery requires understanding:
- Ownership
- Lifetime
- Concurrency
- Trade-offs

This document is a **simple, complete, lifelong reference**.

---

<a id="part-2"></a>

*Part 2 — source: `cpp11_cpp14_complete_reference.md`*

## C++11 and C++14 — Complete Language Reference

### Introduction

C++11 and C++14 fundamentally modernized C++.

They introduced:
- Deterministic resource management with safer semantics
- True concurrency support
- Move semantics
- Compile-time computation
- Expressive generic programming
- Performance without abstraction cost

This document is a **complete, no-compromise reference**.

---

## PART I — CORE LANGUAGE FUNDAMENTALS

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

---

### Types and Value Categories

#### Fundamental Types

```cpp
bool
char / wchar_t / char16_t / char32_t
int / short / long / long long
float / double / long double
void
nullptr_t
```

---

#### Value Categories (CRITICAL)

| Category | Meaning |
|--------|--------|
| lvalue | Has identity |
| xvalue | Expiring value |
| prvalue | Temporary |
| glvalue | lvalue + xvalue |
| rvalue | prvalue + xvalue |

```cpp
int x = 10;        // lvalue
int&& r = 10;     // rvalue reference
```

---

### const, constexpr, and literal types

```cpp
const int x = runtime();
constexpr int y = 10;
```

C++14 allows constexpr with:
- Loops
- Conditionals
- Multiple statements

---

## PART II — OBJECT LIFETIME & MEMORY MODEL

### Object Lifetime

Phases:
1. Storage allocation
2. Construction
3. Usage
4. Destruction
5. Storage deallocation

---

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

---

### RAII (FOUNDATIONAL)

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

---

### Rule of 0 / 3 / 5

#### Rule of 0 (preferred)

```cpp
class A {
    std::unique_ptr<int> p;
};
```

---

### Copy Semantics

```cpp
A(const A&);
A& operator=(const A&);
```

---

### Move Semantics (C++11 CORE FEATURE)

```cpp
A(A&&) noexcept;
A& operator=(A&&) noexcept;
```

```cpp
std::vector<int> v2 = std::move(v1);
```

---

### std::move vs std::forward

```cpp
std::move(x);        // cast to rvalue
std::forward<T>(x);  // perfect forwarding
```

---

## PART III — SMART POINTERS

### unique_ptr

```cpp
std::unique_ptr<int> p = std::make_unique<int>(5);
```

- Exclusive ownership
- Zero overhead

---

### shared_ptr

```cpp
auto p = std::make_shared<int>(10);
```

- Reference counting
- Atomic overhead

---

### weak_ptr

```cpp
std::weak_ptr<int> wp;
```

- Breaks cycles
- Observational access

---

## PART IV — EXPRESSIVE LANGUAGE FEATURES

### auto Type Deduction

```cpp
auto x = 10;
auto y = foo();
```

---

### decltype

```cpp
decltype(x) y;
```

---

### Range-based for

```cpp
for (auto& e : container) {}
```

---

### Uniform Initialization

```cpp
int x{10};
std::vector<int> v{1,2,3};
```

---

### nullptr

```cpp
int* p = nullptr;
```

---

## PART V — LAMBDAS (C++11 & 14)

### Lambda Basics

```cpp
auto f = [](int x){ return x*x; };
```

---

### Capture Modes

```cpp
[x]   // by value
[&x]  // by reference
[=]   // all by value
[&]   // all by reference
```

---

### Generic Lambdas (C++14)

```cpp
auto add = [](auto a, auto b) {
    return a + b;
};
```

---

## PART VI — TEMPLATES & GENERIC PROGRAMMING

### Function Templates

```cpp
template<typename T>
T add(T a, T b);
```

---

### Class Templates

```cpp
template<typename T>
class Box { T value; };
```

---

### Variadic Templates

```cpp
template<typename... Args>
void log(Args... args);
```

---

### Perfect Forwarding

```cpp
template<typename T>
void wrapper(T&& x) {
    f(std::forward<T>(x));
}
```

---

## PART VII — CONCURRENCY (C++11)

### std::thread

```cpp
std::thread t(f);
t.join();
```

---

### Mutexes

```cpp
std::mutex m;
std::lock_guard<std::mutex> lock(m);
```

---

### Condition Variables

```cpp
std::condition_variable cv;
```

---

### Atomics

```cpp
std::atomic<int> x{0};
```

Memory ordering:
- relaxed
- acquire
- release
- seq_cst

---

## PART VIII — STL MODERNIZATION

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

---

### Algorithms

```cpp
std::sort
std::find
std::count_if
std::transform
```

---

### Iterator Categories

- input
- output
- forward
- bidirectional
- random-access

---

## PART IX — constexpr (C++14 EXPANDED)

```cpp
constexpr int factorial(int n) {
    int r = 1;
    for (int i = 1; i <= n; ++i)
        r *= i;
    return r;
}
```

---

## PART X — ERROR HANDLING

### Exceptions

```cpp
try {
    throw std::runtime_error("err");
} catch (...) {}
```

---

### noexcept

```cpp
void f() noexcept;
```

Used for:
- Optimization
- Move semantics safety

---

## PART XI — LOW-LEVEL & SYSTEM FEATURES

### Alignment

```cpp
alignas(64) int x;
```

---

### Type Traits

```cpp
std::is_integral<T>::value
```

---

### chrono Library

```cpp
using namespace std::chrono;
```

---

## PART XII — C++14 IMPROVEMENTS SUMMARY

C++14 added:
- Generic lambdas
- constexpr expansion
- Return type deduction
- make_unique
- Binary literals
- Digit separators

```cpp
int x = 0b1010;
int y = 1'000'000;
```

---

## PART XIII — PERFORMANCE & DESIGN GUIDELINES

### Zero-Cost Abstractions

Abstractions must not cost runtime overhead.

---

### Avoiding Undefined Behavior

- No dangling references
- No data races
- No double delete
- No out-of-bounds access

---

### Best Practices

- Prefer value semantics
- Prefer RAII
- Avoid raw new/delete
- Use standard library
- Measure before optimizing

---

## PART XIV — FINAL SUMMARY

C++11 and C++14 transformed C++ into:
- A safe systems language
- A high-performance concurrency platform
- A modern expressive language

Mastery requires:
- Understanding lifetimes
- Understanding ownership
- Understanding concurrency
- Understanding trade-offs

This document is a **complete foundational reference**.

---

<a id="part-3"></a>

*Part 3 — source: `cpp14_new_features_and_constexpr.md`*

## C++14 New Features and constexpr Explained

### Introduction

C++14 is a **refinement release** over C++11.
It does not introduce radical new concepts, but it:

- Simplifies C++11 features
- Reduces boilerplate
- Improves compile-time programming
- Makes modern C++ more practical in real projects

This document explains:
- What is new in C++14
- Why `constexpr` is important and widely used

---

### What Is New in C++14

#### 1. Auto Return Type Deduction for Functions

C++14 allows the compiler to deduce function return types automatically.

```cpp
auto add(int a, int b) {
    return a + b;
}
```

Benefits:
- Less verbose
- Cleaner APIs
- Easier refactoring

---

#### 2. Generic Lambdas

C++14 allows `auto` in lambda parameters.

```cpp
auto sum = [](auto a, auto b) {
    return a + b;
};
```

Benefits:
- Type-independent lambdas
- Reusable functional code
- Simplifies templates

---

#### 3. Lambda Return Type Deduction

Return type is deduced automatically.

```cpp
auto absVal = [](int x) {
    if (x < 0) return -x;
    return x;
};
```

---

#### 4. More Powerful constexpr (Major Improvement)

C++14 removes many restrictions from C++11 constexpr.

Now allowed:
- Local variables
- Loops
- Conditionals

```cpp
constexpr int factorial(int n) {
    int result = 1;
    for (int i = 1; i <= n; ++i)
        result *= i;
    return result;
}
```

---

#### 5. std::make_unique (New Utility)

C++14 introduces `std::make_unique`.

```cpp
#include <memory>

auto p = std::make_unique<int>(10);
```

Benefits:
- Prevents memory leaks
- Exception-safe
- Encourages unique ownership

---

#### 6. Variable Templates

Templates for variables.

```cpp
template<typename T>
constexpr T pi = T(

```

---

<a id="part-4"></a>

*Part 4 — source: `modern_cpp_utilities.md`*

## Modern C++ Utilities (C++11–C++14)

### Introduction

Modern C++ (C++11 and C++14) introduced a rich set of **utilities** that enable:

- Type safety
- Compile-time computation
- Zero-overhead abstractions
- Safer memory management
- Better concurrency support

This document summarizes the **most important utilities** used in real-world systems and interviews.

---

### Container Utilities

#### std::array

Fixed-size container with STL support.

```cpp
#include <array>

std::array<int, 3> a = {1, 2, 3};
```

Benefits:
- Stack allocated
- Knows its size
- Works with STL algorithms
- constexpr-friendly

---

#### std::vector

Dynamic array.

```cpp
#include <vector>

std::vector<int> v = {1, 2, 3};
```

Use when size is not known at compile time.

---

#### std::pair

Two heterogeneous values.

```cpp
std::pair<int, int> p = {10, 20};
```

Used in maps, ranges, return values.

---

#### std::tuple

Multiple heterogeneous values.

```cpp
#include <tuple>

auto t = std::make_tuple(1, 3.14, 'a');
```

Often used to return multiple values.

---

### Memory & Ownership Utilities

#### std::unique_ptr

Exclusive ownership smart pointer.

```cpp
#include <memory>

auto p = std::make_unique<int>(10);
```

Rules:
- Cannot be copied
- Can be moved
- Zero overhead

---

#### std::shared_ptr

Shared ownership smart pointer.

```cpp
auto sp = std::make_shared<int>(20);
```

Reference-counted ownership.

---

#### std::weak_ptr

Non-owning reference to shared_ptr.

```cpp
std::weak_ptr<int> wp;
```

Prevents circular references.

---

### Functional Utilities

#### std::function

Type-erased callable wrapper.

```cpp
#include <functional>

std::function<int(int,int)> add =
    [](int a, int b){ return a + b; };
```

Used for callbacks and APIs.

---

#### std::bind

Argument binding utility.

```cpp
auto add5 = std::bind(add, 5, std::placeholders::_1);
```

Note: Lambdas are preferred.

---

### Type System & Metaprogramming

#### decltype

Deduces type of an expression.

```cpp
int x = 10;
decltype(x) y = 20;
```

---

#### auto

Type inference.

```cpp
auto x = 10;
auto y = 3.14;
```

Improves readability and reduces verbosity.

---

#### <type_traits>

Compile-time type inspection.

```cpp
#include <type_traits>

static_assert(std::is_integral<int>::value, "Not integral");
```

Used heavily in templates.

---

#### std::integer_sequence (C++14)

Compile-time index generation.

```cpp
std::index_sequence<0,1,2>
```

Used in tuple unpacking.

---

### Compile-Time Utilities

#### constexpr

Allows compile-time evaluation.

```cpp
constexpr int square(int x) {
    return x * x;
}
```

C++14 allows loops and conditionals.

---

#### static_assert

Compile-time assertion.

```cpp
static_assert(sizeof(int) == 4, "Unexpected int size");
```

---

#### std::numeric_limits

Type limits and properties.

```cpp
#include <limits>

int maxInt = std::numeric_limits<int>::max();
```

Used for overflow protection and numeric safety.

---

### Time Utilities

#### std::chrono

Type-safe time handling.

```cpp
#include <chrono>

using namespace std::chrono;
seconds s = 5s;
milliseconds ms = 500ms;
```

---

#### Measuring execution time

```cpp
auto start = std::chrono::high_resolution_clock::now();
// work
auto end = std::chrono::high_resolution_clock::now();
```

---

### Concurrency Utilities

#### std::thread

Create threads.

```cpp
#include <thread>

std::thread t([]{
    // work
});
t.join();
```

---

#### std::mutex and std::lock_guard

Synchronization primitives.

```cpp
#include <mutex>

std::mutex m;
std::lock_guard<std::mutex> lock(m);
```

RAII-based locking.

---

### Random Utilities

#### <random>

Modern random number generation.

```cpp
#include <random>

std::mt19937 gen;
std::uniform_int_distribution<> dist(1, 6);
```

Avoids problems of rand().

---

### Move Semantics Utilities

#### std::move

Enables move semantics.

```cpp
std::vector<int> v2 = std::move(v1);
```

Transfers ownership.

---

#### std::forward

Perfect forwarding.

```cpp
template<typename T>
void f(T&& t) {
    g(std::forward<T>(t));
}
```

Used in templates.

---

### Initialization Utilities

#### std::initializer_list

Uniform initialization.

```cpp
std::vector<int> v = {1, 2, 3};
```

---

### Exception & Optimization Utilities

#### noexcept

Indicates function does not throw.

```cpp
void foo() noexcept;
```

Improves optimization and correctness.

---

### Most Important Utilities (Interview Focus)

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

### Final Takeaway

Modern C++ utilities allow you to write:

- Faster code
- Safer code
- Cleaner abstractions

All **without runtime overhead**.

Use them correctly — and C++ becomes a powerful, expressive language.
