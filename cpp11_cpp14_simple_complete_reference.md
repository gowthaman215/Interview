# C++11 and C++14 — Simple Complete Reference

## 1. Introduction

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

## 2. Basic Language Elements

### Program Structure

```cpp
#include <iostream>

int main() {
    std::cout << "Hello";
}
```

Execution starts at `main()`.

---

### Variables and Types

```cpp
int a = 10;
double b = 3.14;
bool ok = true;
```

C++ is statically typed.

---

### const vs constexpr

```cpp
const int x = runtime();
constexpr int y = 10;
```

- `const` → runtime constant
- `constexpr` → compile-time constant

C++14 allows loops inside `constexpr`.

---

## 3. Value Categories (Very Important)

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

## 4. Pointers and References

```cpp
int x = 10;
int* p = &x;
int& r = x;
```

- Pointer → address
- Reference → alias

---

## 5. Memory and Lifetime

### Stack vs Heap

```cpp
int a = 10;
int* p = new int(5);
delete p;
```

Prefer stack and RAII.

---

## 6. RAII (FOUNDATION)

```cpp
#include <fstream>

void write() {
    std::ofstream f("data.txt");
    f << "Hello";
} // file closed automatically
```

RAII guarantees cleanup.

---

## 7. Object Lifecycle

### Constructors and Destructors

```cpp
class A {
public:
    A() {}
    ~A() {}
};
```

Destructors are called automatically.

---

## 8. Rule of 0 / 3 / 5

- Rule of 0 → Use RAII types
- Rule of 3 → Destructor, copy ctor, copy assign
- Rule of 5 → + move ctor, move assign

```cpp
class Safe {
    std::unique_ptr<int> p;
};
```

---

## 9. Move Semantics (C++11 Core)

```cpp
std::vector<int> v1 = {1,2};
std::vector<int> v2 = std::move(v1);
```

Moves ownership instead of copying.

---

## 10. std::move vs std::forward

```cpp
std::move(x);        // cast to rvalue
std::forward<T>(x);  // preserve value category
```

---

## 11. Smart Pointers

### unique_ptr

```cpp
auto p = std::make_unique<int>(10);
```

Exclusive ownership.

---

### shared_ptr

```cpp
auto p = std::make_shared<int>(10);
```

Reference-counted ownership.

---

### weak_ptr

```cpp
std::weak_ptr<int> wp;
```

Breaks cycles.

---

## 12. auto and decltype

```cpp
auto x = 10;
decltype(x) y = 20;
```

---

## 13. Uniform Initialization

```cpp
int x{10};
std::vector<int> v{1,2,3};
```

Prevents narrowing.

---

## 14. Lambdas (C++11 / C++14)

### Basic Lambda

```cpp
auto f = [](int x) { return x*x; };
```

---

### Capture

```cpp
[x]  // by value
[&x] // by reference
```

---

### Generic Lambda (C++14)

```cpp
auto add = [](auto a, auto b) {
    return a + b;
};
```

---

## 15. Templates

### Function Template

```cpp
template<typename T>
T add(T a, T b);
```

---

### Class Template

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

## 16. STL Containers

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

## 17. STL Algorithms

```cpp
std::sort(v.begin(), v.end());
std::find(v.begin(), v.end(), 5);
```

Prefer algorithms over loops.

---

## 18. Iterators

```cpp
for (auto it = v.begin(); it != v.end(); ++it) {}
```

---

## 19. Multithreading (C++11)

### std::thread

```cpp
std::thread t(f);
t.join();
```

---

### mutex

```cpp
std::mutex m;
std::lock_guard<std::mutex> lock(m);
```

---

### condition_variable

```cpp
std::condition_variable cv;
```

---

### atomic

```cpp
std::atomic<int> x{0};
x++;
```

---

## 20. Memory Model (Simple View)

- Data race → undefined behavior
- Mutex / atomic → synchronization
- Happens-before rules

---

## 21. chrono (Time)

```cpp
using namespace std::chrono;
auto t = steady_clock::now();
```

---

## 22. constexpr (C++14 Expanded)

```cpp
constexpr int fact(int n) {
    int r = 1;
    for(int i=1;i<=n;i++) r*=i;
    return r;
}
```

---

## 23. noexcept

```cpp
void f() noexcept;
```

Required for safe move operations.

---

## 24. Type Traits

```cpp
std::is_integral<int>::value
```

Used in templates.

---

## 25. Low-Level Features

### Alignment

```cpp
alignas(64) int x;
```

---

## 26. Error Handling

```cpp
try {
    throw std::runtime_error("err");
} catch(...) {}
```

---

## 27. C++14 Additions Summary

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

## 28. Performance Principles

- Prefer stack
- Avoid heap in hot paths
- Avoid virtual calls in critical loops
- Use cache-friendly data
- Measure before optimizing

---

## 29. Common Undefined Behavior

- Dangling reference
- Double delete
- Data race
- Out-of-bounds access
- Using moved-from object

---

## 30. Final Takeaway

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
