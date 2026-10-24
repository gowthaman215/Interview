# C++ Templates and STL — Complete In-Depth Reference

## 1. Introduction

The C++ Standard Template Library (STL) is built entirely on **templates**.
To understand STL properly, you must first understand **template-based generic programming**.

STL provides:
- Containers (data storage)
- Algorithms (data processing)
- Iterators (abstraction layer)
- Allocators (memory control)

All of these are template-based and zero-cost.

---

## 2. Templates — The Foundation of STL

Templates enable **generic programming**:
> Write code once, use it for any type.

STL would not exist without templates.

---

## 3. Function Templates

### Basic Function Template

```cpp
template<typename T>
T add(T a, T b) {
    return a + b;
}
```

Compiler generates functions as needed:

```cpp
add<int>(1, 2);
add(1.5, 2.5);   // type deduced
```

---

### Multiple Template Parameters

```cpp
template<typename T, typename U>
auto multiply(T a, U b) {
    return a * b;
}
```

---

## 4. Class Templates

### Basic Class Template

```cpp
template<typename T>
class Box {
    T value;
public:
    Box(T v) : value(v) {}
    T get() const { return value; }
};
```

Usage:

```cpp
Box<int> bi(10);
Box<std::string> bs("hello");
```

Every STL container is a **class template**.

---

## 5. Template Specialization

### Full Specialization

```cpp
template<>
class Box<bool> {
public:
    Box(bool) {}
};
```

Used when a specific type needs special handling.

---

### Partial Specialization (Class Templates Only)

```cpp
template<typename T>
class Box<T*> {
};
```

STL uses partial specialization heavily.

---

## 6. Variadic Templates (C++11)

```cpp
template<typename... Args>
void log(Args... args) {}
```

Used in:
- std::tuple
- std::function
- std::emplace

---

## 7. Perfect Forwarding

```cpp
template<typename T>
void wrapper(T&& arg) {
    process(std::forward<T>(arg));
}
```

Preserves value category:
- lvalue → lvalue
- rvalue → rvalue

Critical for STL efficiency.

---

## 8. STL Overview

STL consists of:

- Containers
- Iterators
- Algorithms
- Function objects
- Allocators

Algorithms operate on **iterator ranges**, not containers.

---

## 9. Sequence Containers

### std::vector

```cpp
std::vector<int> v = {1,2,3};
v.push_back(4);
```

Properties:
- Contiguous memory
- Cache friendly
- O(1) random access
- Insertion in middle is expensive

---

### std::deque

```cpp
std::deque<int> d;
d.push_front(1);
d.push_back(2);
```

- Fast front/back insertion
- Non-contiguous memory

---

### std::list

```cpp
std::list<int> l;
l.push_back(1);
```

- O(1) insertion anywhere
- Poor cache locality
- Rarely needed

---

### std::array

```cpp
std::array<int,3> a = {1,2,3};
```

- Fixed size
- Stack allocated
- Zero overhead

---

## 10. Associative Containers (Ordered)

Based on balanced trees (Red-Black Tree).

---

### std::map

```cpp
std::map<int,std::string> m;
m[1] = "one";
```

Properties:
- Sorted by key
- O(log n) operations

---

### std::set

```cpp
std::set<int> s = {1,2,3};
```

Unique sorted keys.

---

## 11. Unordered Containers (Hash-Based)

Based on hash tables.

---

### std::unordered_map

```cpp
std::unordered_map<int,std::string> um;
um[1] = "one";
```

Properties:
- O(1) average lookup
- No ordering
- Higher memory usage

---

## 12. Iterators — The Glue

Iterators are generalized pointers.

```cpp
for(auto it = v.begin(); it != v.end(); ++it) {
    std::cout << *it;
}
```

Algorithms only see iterators.

---

## 13. Iterator Categories

| Category | Capability |
|-------|-----------|
| Input | Read once |
| Output | Write once |
| Forward | Multi-pass |
| Bidirectional | ++, -- |
| Random Access | +, -, [] |

---

## 14. STL Algorithms

Algorithms operate on iterator ranges.

```cpp
std::sort(v.begin(), v.end());
std::find(v.begin(), v.end(), 3);
std::count_if(v.begin(), v.end(),
              [](int x){ return x > 2; });
```

Algorithms do NOT know container types.

---

## 15. Function Objects (Functors)

```cpp
struct Greater {
    bool operator()(int a, int b) const {
        return a > b;
    }
};
```

Used by algorithms and containers.

---

## 16. Emplace vs Insert (C++11)

```cpp
v.emplace_back(10);  // constructs in place
v.push_back(10);     // may create temporary
```

Emplace avoids unnecessary copies.

---

## 17. Allocators (Advanced)

Allocators control memory behavior.

```cpp
std::vector<int, MyAllocator<int>> v;
```

Used in:
- Custom memory pools
- Low-latency systems
- Embedded systems

---

## 18. Iterator Invalidation Rules

Example:

```cpp
std::vector<int> v = {1,2};
auto it = v.begin();
v.push_back(3);   // it may be invalid
```

Key idea:
- vector reallocates
- list does not
- map invalidates erased elements only

---

## 19. STL Complexity Guarantees

STL guarantees performance.

Examples:
- vector::push_back → amortized O(1)
- map::find → O(log n)
- unordered_map::find → O(1) average

Design depends on these guarantees.

---

## 20. STL Trade-Offs (Critical Thinking)

| Choice | Gain | Cost |
|-----|-----|-----|
| vector | Cache friendly | Slow inserts |
| list | Fast inserts | Poor cache |
| map | Ordered | Slower |
| unordered_map | Fast | Memory |

STL forces conscious design.

---

## 21. STL + RAII

```cpp
{
    std::vector<int> v = {1,2,3};
} // memory released automatically
```

STL containers fully follow RAII.

---

## 22. STL in Modern C++11/14

Improvements:
- Move semantics reduce copies
- Lambdas replace functors
- auto simplifies code
- make_unique added
- constexpr containers (limited)

---

## 23. Common STL Mistakes

- Using list without reason
- Ignoring iterator invalidation
- Copying large containers
- Not reserving vector capacity
- Overusing unordered containers

---

## 24. Real-World STL Usage Guidelines

- Prefer vector by default
- Reserve capacity early
- Avoid list unless necessary
- Use algorithms instead of loops
- Understand memory layout
- Measure performance

---

## 25. Final Takeaway

STL is not just a library.
It is a **generic programming framework** built on templates, iterators, and strict performance guarantees.

Mastery requires understanding:
- Templates
- Ownership
- Iterators
- Complexity
- Trade-offs

This document is a **complete, simple, long-term STL reference**.
