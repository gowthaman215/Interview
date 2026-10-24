# C++ Templates and STL

This file combines the following references into one document:

1. [C++ Templates and STL — Complete In-Depth Reference](#part-1) — from `cpp_templates_and_stl_complete_reference.md`
2. [C++ STL Containers — Complete Reference with Member Function Examples](#part-2) — from `cpp_stl_containers_complete_reference.md`
3. [C++ STL Algorithms — Complete Reference with Examples](#part-3) — from `cpp_stl_algorithms_complete_reference.md`
4. [STL Trade-offs in C++](#part-4) — from `stl_tradeoffs.md`

---

<a id="part-1"></a>

*Part 1 — source: `cpp_templates_and_stl_complete_reference.md`*

## C++ Templates and STL — Complete In-Depth Reference

### 1. Introduction

The C++ Standard Template Library (STL) is built entirely on **templates**.
To understand STL properly, you must first understand **template-based generic programming**.

STL provides:
- Containers (data storage)
- Algorithms (data processing)
- Iterators (abstraction layer)
- Allocators (memory control)

All of these are template-based and zero-cost.

---

### 2. Templates — The Foundation of STL

Templates enable **generic programming**:
> Write code once, use it for any type.

STL would not exist without templates.

---

### 3. Function Templates

#### Basic Function Template

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

#### Multiple Template Parameters

```cpp
template<typename T, typename U>
auto multiply(T a, U b) {
    return a * b;
}
```

---

### 4. Class Templates

#### Basic Class Template

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

### 5. Template Specialization

#### Full Specialization

```cpp
template<>
class Box<bool> {
public:
    Box(bool) {}
};
```

Used when a specific type needs special handling.

---

#### Partial Specialization (Class Templates Only)

```cpp
template<typename T>
class Box<T*> {
};
```

STL uses partial specialization heavily.

---

### 6. Variadic Templates (C++11)

```cpp
template<typename... Args>
void log(Args... args) {}
```

Used in:
- std::tuple
- std::function
- std::emplace

---

### 7. Perfect Forwarding

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

### 8. STL Overview

STL consists of:

- Containers
- Iterators
- Algorithms
- Function objects
- Allocators

Algorithms operate on **iterator ranges**, not containers.

---

### 9. Sequence Containers

#### std::vector

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

#### std::deque

```cpp
std::deque<int> d;
d.push_front(1);
d.push_back(2);
```

- Fast front/back insertion
- Non-contiguous memory

---

#### std::list

```cpp
std::list<int> l;
l.push_back(1);
```

- O(1) insertion anywhere
- Poor cache locality
- Rarely needed

---

#### std::array

```cpp
std::array<int,3> a = {1,2,3};
```

- Fixed size
- Stack allocated
- Zero overhead

---

### 10. Associative Containers (Ordered)

Based on balanced trees (Red-Black Tree).

---

#### std::map

```cpp
std::map<int,std::string> m;
m[1] = "one";
```

Properties:
- Sorted by key
- O(log n) operations

---

#### std::set

```cpp
std::set<int> s = {1,2,3};
```

Unique sorted keys.

---

### 11. Unordered Containers (Hash-Based)

Based on hash tables.

---

#### std::unordered_map

```cpp
std::unordered_map<int,std::string> um;
um[1] = "one";
```

Properties:
- O(1) average lookup
- No ordering
- Higher memory usage

---

### 12. Iterators — The Glue

Iterators are generalized pointers.

```cpp
for(auto it = v.begin(); it != v.end(); ++it) {
    std::cout << *it;
}
```

Algorithms only see iterators.

---

### 13. Iterator Categories

| Category | Capability |
|-------|-----------|
| Input | Read once |
| Output | Write once |
| Forward | Multi-pass |
| Bidirectional | ++, -- |
| Random Access | +, -, [] |

---

### 14. STL Algorithms

Algorithms operate on iterator ranges.

```cpp
std::sort(v.begin(), v.end());
std::find(v.begin(), v.end(), 3);
std::count_if(v.begin(), v.end(),
              [](int x){ return x > 2; });
```

Algorithms do NOT know container types.

---

### 15. Function Objects (Functors)

```cpp
struct Greater {
    bool operator()(int a, int b) const {
        return a > b;
    }
};
```

Used by algorithms and containers.

---

### 16. Emplace vs Insert (C++11)

```cpp
v.emplace_back(10);  // constructs in place
v.push_back(10);     // may create temporary
```

Emplace avoids unnecessary copies.

---

### 17. Allocators (Advanced)

Allocators control memory behavior.

```cpp
std::vector<int, MyAllocator<int>> v;
```

Used in:
- Custom memory pools
- Low-latency systems
- Embedded systems

---

### 18. Iterator Invalidation Rules

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

### 19. STL Complexity Guarantees

STL guarantees performance.

Examples:
- vector::push_back → amortized O(1)
- map::find → O(log n)
- unordered_map::find → O(1) average

Design depends on these guarantees.

---

### 20. STL Trade-Offs (Critical Thinking)

| Choice | Gain | Cost |
|-----|-----|-----|
| vector | Cache friendly | Slow inserts |
| list | Fast inserts | Poor cache |
| map | Ordered | Slower |
| unordered_map | Fast | Memory |

STL forces conscious design.

---

### 21. STL + RAII

```cpp
{
    std::vector<int> v = {1,2,3};
} // memory released automatically
```

STL containers fully follow RAII.

---

### 22. STL in Modern C++11/14

Improvements:
- Move semantics reduce copies
- Lambdas replace functors
- auto simplifies code
- make_unique added
- constexpr containers (limited)

---

### 23. Common STL Mistakes

- Using list without reason
- Ignoring iterator invalidation
- Copying large containers
- Not reserving vector capacity
- Overusing unordered containers

---

### 24. Real-World STL Usage Guidelines

- Prefer vector by default
- Reserve capacity early
- Avoid list unless necessary
- Use algorithms instead of loops
- Understand memory layout
- Measure performance

---

### 25. Final Takeaway

STL is not just a library.
It is a **generic programming framework** built on templates, iterators, and strict performance guarantees.

Mastery requires understanding:
- Templates
- Ownership
- Iterators
- Complexity
- Trade-offs

This document is a **complete, simple, long-term STL reference**.

---

<a id="part-2"></a>

*Part 2 — source: `cpp_stl_containers_complete_reference.md`*

## C++ STL Containers — Complete Reference with Member Function Examples

### 1. STL Containers Overview

STL containers are **template-based data structures** that manage collections of objects.

Container categories:
- Sequence containers
- Associative containers (ordered)
- Unordered containers (hash-based)
- Container adapters

All containers follow RAII and manage memory automatically.

---

### 2. Sequence Containers

Sequence containers store elements in a linear order.

---

#### 2.1 std::vector

Dynamic array with contiguous memory.

##### Common Member Functions

```cpp
std::vector<int> v;

// capacity
v.size();
v.empty();
v.capacity();
v.reserve(100);
v.shrink_to_fit();

// element access
v[0];
v.at(0);
v.front();
v.back();
v.data();

// modifiers
v.push_back(10);
v.emplace_back(20);
v.pop_back();
v.insert(v.begin(), 5);
v.erase(v.begin());
v.clear();

// iterators
v.begin();
v.end();
```

Key notes:
- Fast random access
- Insertion in middle is expensive
- Iterators invalidated on reallocation

---

#### 2.2 std::deque

Double-ended queue.

##### Common Member Functions

```cpp
std::deque<int> d;

d.push_back(1);
d.push_front(2);
d.pop_back();
d.pop_front();

d.front();
d.back();

d.size();
d.clear();
```

Key notes:
- Fast insertion at both ends
- No contiguous memory
- Iterators stable unless reallocation of blocks

---

#### 2.3 std::list

Doubly linked list.

##### Common Member Functions

```cpp
std::list<int> l;

l.push_back(1);
l.push_front(2);

l.insert(l.begin(), 5);
l.erase(l.begin());

l.remove(2);
l.sort();
l.reverse();
l.unique();
```

Key notes:
- Fast insertion anywhere
- No random access
- Poor cache locality

---

#### 2.4 std::forward_list

Singly linked list.

##### Common Member Functions

```cpp
std::forward_list<int> fl;

fl.push_front(1);
fl.insert_after(fl.before_begin(), 2);
fl.erase_after(fl.before_begin());
```

Key notes:
- Minimal memory overhead
- No size()
- One-direction traversal

---

#### 2.5 std::array

Fixed-size container.

##### Common Member Functions

```cpp
std::array<int, 3> a = {1,2,3};

a.size();
a.empty();

a[0];
a.at(1);
a.front();
a.back();
```

Key notes:
- Stack allocated
- Size known at compile time
- Zero overhead

---

### 3. Associative Containers (Ordered)

Implemented using balanced trees (Red-Black Tree).

---

#### 3.1 std::map

Key-value pairs, sorted by key.

##### Common Member Functions

```cpp
std::map<int, std::string> m;

// insert
m.insert({1, "one"});
m.emplace(2, "two");

// access
m[1];
m.at(2);

// lookup
m.find(1);
m.count(2);

// erase
m.erase(1);
m.clear();
```

Key notes:
- Keys are unique
- O(log n) operations
- Iterators stable

---

#### 3.2 std::multimap

Allows duplicate keys.

```cpp
std::multimap<int, std::string> mm;
mm.insert({1, "one"});
mm.insert({1, "uno"});
```

---

#### 3.3 std::set

Sorted unique keys.

##### Common Member Functions

```cpp
std::set<int> s;

s.insert(3);
s.emplace(1);

s.find(1);
s.count(3);

s.erase(3);
```

---

#### 3.4 std::multiset

Allows duplicate keys.

```cpp
std::multiset<int> ms;
ms.insert(1);
ms.insert(1);
```

---

### 4. Unordered Containers (Hash-Based)

Implemented using hash tables.

---

#### 4.1 std::unordered_map

##### Common Member Functions

```cpp
std::unordered_map<int, std::string> um;

// insert
um.emplace(1, "one");

// access
um[1];
um.at(1);

// lookup
um.find(1);
um.count(1);

// bucket control
um.bucket_count();
um.load_factor();
um.rehash(100);
```

Key notes:
- O(1) average lookup
- No ordering
- Iterators invalidated on rehash

---

#### 4.2 std::unordered_multimap

Allows duplicate keys.

```cpp
std::unordered_multimap<int, std::string> umm;
```

---

#### 4.3 std::unordered_set

```cpp
std::unordered_set<int> us;

us.insert(1);
us.find(1);
us.erase(1);
```

---

#### 4.4 std::unordered_multiset

```cpp
std::unordered_multiset<int> ums;
```

---

### 5. Container Adapters

Adapters provide restricted interfaces.

---

#### 5.1 std::stack

LIFO container.

```cpp
std::stack<int> st;

st.push(1);
st.top();
st.pop();
st.empty();
```

---

#### 5.2 std::queue

FIFO container.

```cpp
std::queue<int> q;

q.push(1);
q.front();
q.back();
q.pop();
```

---

#### 5.3 std::priority_queue

Heap-based queue.

```cpp
std::priority_queue<int> pq;

pq.push(10);
pq.push(5);
pq.top();  // largest
pq.pop();
```

Min-heap:

```cpp
std::priority_queue<int, std::vector<int>, std::greater<int>> minpq;
```

---

### 6. Common Container Member Functions

Most containers support:

```cpp
size();
empty();
clear();
begin();
end();
cbegin();
cend();
```

---

### 7. Iterator Invalidation Summary

| Container | Invalidation |
|--------|-------------|
| vector | On reallocation |
| deque | On insertion at ends |
| list | Never (except erased) |
| map | Never (except erased) |
| unordered_map | On rehash |

---

### 8. Container Selection Guidelines

- Default → vector
- Need ordering → map / set
- Need fast lookup → unordered_map
- Frequent insert anywhere → list
- Fixed size → array

---

### 9. Common Mistakes

- Using list without need
- Forgetting reserve() for vector
- Ignoring iterator invalidation
- Using unordered containers without hash tuning

---

### 10. Final Takeaway

STL containers provide safe, efficient, and well-defined data structures.
Choosing the correct container and using the right member functions is critical for performance and correctness in modern C++.

---

<a id="part-3"></a>

*Part 3 — source: `cpp_stl_algorithms_complete_reference.md`*

## C++ STL Algorithms — Complete Reference with Examples

### 1. Introduction to STL Algorithms

STL algorithms are **generic functions** that operate on **iterator ranges**.

Key principles:
- Algorithms do NOT own data
- They work on iterators
- Containers and algorithms are decoupled
- Performance guarantees are well-defined

Most algorithms are declared in:
```cpp
#include <algorithm>
#include <numeric>
```

---

### 2. Iterator-Based Design

All algorithms operate on:
```cpp
[first, last)
```

Where:
- `first` → inclusive
- `last` → exclusive

```cpp
std::sort(v.begin(), v.end());
```

---

### 3. Non-Modifying Sequence Algorithms

These algorithms **do not change elements**.

---

#### std::find

```cpp
auto it = std::find(v.begin(), v.end(), 5);
```

Finds first occurrence.

---

#### std::count

```cpp
std::count(v.begin(), v.end(), 1);
```

---

#### std::count_if

```cpp
std::count_if(v.begin(), v.end(),
              [](int x){ return x > 10; });
```

---

#### std::find_if / find_if_not

```cpp
std::find_if(v.begin(), v.end(), pred);
```

---

#### std::all_of / any_of / none_of

```cpp
std::all_of(v.begin(), v.end(), pred);
std::any_of(v.begin(), v.end(), pred);
std::none_of(v.begin(), v.end(), pred);
```

---

### 4. Modifying Sequence Algorithms

These algorithms **modify elements**.

---

#### std::copy

```cpp
std::copy(src.begin(), src.end(), dest.begin());
```

---

#### std::move

```cpp
std::move(src.begin(), src.end(), dest.begin());
```

Moves elements.

---

#### std::transform

```cpp
std::transform(v.begin(), v.end(), v.begin(),
               [](int x){ return x * 2; });
```

---

#### std::replace / replace_if

```cpp
std::replace(v.begin(), v.end(), 1, 100);
```

---

#### std::fill

```cpp
std::fill(v.begin(), v.end(), 0);
```

---

#### std::remove / remove_if (IMPORTANT)

```cpp
auto it = std::remove(v.begin(), v.end(), 3);
v.erase(it, v.end());
```

- Does NOT erase from container
- Shifts elements

---

### 5. Partitioning Algorithms

---

#### std::partition

```cpp
std::partition(v.begin(), v.end(),
               [](int x){ return x % 2 == 0; });
```

---

#### std::stable_partition

Preserves order.

---

### 6. Sorting Algorithms

---

#### std::sort

```cpp
std::sort(v.begin(), v.end());
```

- Average O(n log n)
- Requires random-access iterators

---

#### std::stable_sort

Preserves relative order.

---

#### std::partial_sort

```cpp
std::partial_sort(v.begin(), v.begin()+3, v.end());
```

---

#### std::nth_element

```cpp
std::nth_element(v.begin(), v.begin()+k, v.end());
```

Places nth element correctly.

---

### 7. Binary Search Algorithms (Sorted Range)

---

#### std::binary_search

```cpp
std::binary_search(v.begin(), v.end(), 10);
```

---

#### std::lower_bound / upper_bound

```cpp
auto it1 = std::lower_bound(v.begin(), v.end(), 10);
auto it2 = std::upper_bound(v.begin(), v.end(), 10);
```

---

#### std::equal_range

```cpp
auto range = std::equal_range(v.begin(), v.end(), 10);
```

---

### 8. Set Algorithms (Sorted Ranges)

---

#### std::set_union

```cpp
std::set_union(a.begin(), a.end(),
               b.begin(), b.end(),
               out.begin());
```

---

#### std::set_intersection

---

#### std::set_difference

---

#### std::set_symmetric_difference

---

### 9. Heap Algorithms

---

#### std::make_heap

```cpp
std::make_heap(v.begin(), v.end());
```

---

#### std::push_heap

```cpp
v.push_back(10);
std::push_heap(v.begin(), v.end());
```

---

#### std::pop_heap

```cpp
std::pop_heap(v.begin(), v.end());
v.pop_back();
```

---

#### std::sort_heap

```cpp
std::sort_heap(v.begin(), v.end());
```

---

### 10. Permutation Algorithms

---

#### std::next_permutation

```cpp
std::next_permutation(v.begin(), v.end());
```

---

#### std::prev_permutation

---

### 11. Numeric Algorithms (<numeric>)

---

#### std::accumulate

```cpp
int sum = std::accumulate(v.begin(), v.end(), 0);
```

---

#### std::inner_product

```cpp
std::inner_product(a.begin(), a.end(), b.begin(), 0);
```

---

#### std::partial_sum

```cpp
std::partial_sum(v.begin(), v.end(), v.begin());
```

---

#### std::adjacent_difference

---

### 12. Min / Max Algorithms

---

#### std::min / max

```cpp
std::min(a, b);
std::max(a, b);
```

---

#### std::min_element / max_element

```cpp
auto it = std::max_element(v.begin(), v.end());
```

---

#### std::minmax / minmax_element

---

### 13. Comparison Algorithms

---

#### std::equal

```cpp
std::equal(a.begin(), a.end(), b.begin());
```

---

#### std::lexicographical_compare

```cpp
std::lexicographical_compare(a.begin(), a.end(),
                             b.begin(), b.end());
```

---

### 14. Search Algorithms

---

#### std::search

```cpp
std::search(v.begin(), v.end(),
            sub.begin(), sub.end());
```

---

#### std::find_end

---

#### std::find_first_of

---

### 15. Algorithms vs Member Functions

Prefer:
```cpp
std::find(v.begin(), v.end(), x);
```

Over:
```cpp
container.find(x);  // only for associative containers
```

Algorithms are more generic.

---

### 16. Algorithm Complexity Guarantees

Examples:
- find → O(n)
- sort → O(n log n)
- binary_search → O(log n)
- remove → O(n)

Always check complexity.

---

### 17. Common Algorithm Mistakes

- Forgetting erase-remove idiom
- Using sort on non-random iterators
- Binary search on unsorted data
- Assuming remove deletes elements
- Ignoring iterator invalidation

---

### 18. Algorithms + Lambdas (Modern C++)

```cpp
std::count_if(v.begin(), v.end(),
              [](int x){ return x > 5; });
```

Lambdas replaced many functors.

---

### 19. When to Use Which Algorithm

- Need search → find / binary_search
- Need reorder → sort / partition
- Need transform → transform
- Need aggregate → accumulate
- Need top-k → nth_element

---

### 20. Final Takeaway

STL algorithms provide powerful, efficient, and reusable operations that separate **what** you do from **how** data is stored.

Mastering algorithms is essential for writing modern, clean, and high-performance C++ code.

---

<a id="part-4"></a>

*Part 4 — source: `stl_tradeoffs.md`*

## STL Trade-offs in C++

### Introduction

STL trade-offs refer to the conscious decisions made when choosing STL containers, algorithms, and utilities by balancing:

- Performance
- Memory usage
- Predictability
- Safety
- Flexibility

No STL container or algorithm is universally best; each choice involves compromises.

---

### vector vs list

| Aspect | vector | list |
|------|--------|------|
| Memory layout | Contiguous | Non-contiguous |
| Cache friendliness | Excellent | Poor |
| Random access | O(1) | Not supported |
| Insert/Delete (middle) | O(n) | O(1) |
| Memory overhead | Low | High |

Trade-off:
- `vector` offers speed and cache locality
- `list` offers efficient insertions/deletions

Preferred choice in most systems: `vector`

---

### vector vs deque

| Aspect | vector | deque |
|------|--------|-------|
| Memory layout | Contiguous | Segmented |
| Push front | Slow | Fast |
| Push back | Fast | Fast |
| Random access | O(1) | O(1) |
| Cache locality | Best | Good |

Trade-off:
- `deque` supports efficient insertion at both ends
- `vector` provides better cache performance

---

### map vs unordered_map

| Aspect | map | unordered_map |
|------|-----|---------------|
| Ordering | Sorted | Unordered |
| Lookup complexity | O(log n) | O(1) average |
| Worst-case | Predictable | Can degrade |
| Memory usage | Lower | Higher |
| Cache friendliness | Poor | Better |

Trade-off:
- `map` provides ordering and predictable performance
- `unordered_map` provides faster average lookup at the cost of memory

---

### set vs unordered_set

| Aspect | set | unordered_set |
|------|-----|---------------|
| Ordering | Sorted | Unordered |
| Lookup complexity | O(log n) | O(1) average |
| Memory usage | Lower | Higher |
| Use case | Ordered unique elements | Fast membership checks |

Trade-off mirrors `map` vs `unordered_map`.

---

### array vs vector

| Aspect | array | vector |
|------|-------|--------|
| Size | Fixed | Dynamic |
| Allocation | Stack | Heap |
| Overhead | None | Small |
| Resize capability | No | Yes |

Trade-off:
- `array` offers predictability and zero overhead
- `vector` offers flexibility and dynamic resizing

Low-latency systems prefer `array` where size is known.

---

### Algorithms vs Custom Loops

```cpp
std::sort(v.begin(), v.end());
```

vs

```cpp
// Custom hand-written loop
```

Trade-off:
- STL algorithms are safer and more readable
- Custom loops allow fine-grained micro-optimizations

Best practice: use STL first, optimize only if required.

---

### Iterators vs Indexing

```cpp
for (auto it = v.begin(); it != v.end(); ++it)
```

vs

```cpp
for (size_t i = 0; i < v.size(); ++i)
```

Trade-off:
- Iterators are generic and container-independent
- Indexing can be simpler and sometimes faster

---

### Smart Pointers in STL Containers

```cpp
std::vector<std::unique_ptr<Foo>>
```

Trade-off:
- Ensures clear ownership and safety
- Introduces slight indirection cost

Modern C++ favors ownership clarity over raw pointers.

---

### Memory vs Speed (Summary)

| Choice | Gain | Cost |
|------|------|------|
| vector | Cache speed | Resize cost |
| list | Fast insertion | Cache misses |
| unordered_map | Fast lookup | Memory usage |
| map | Ordering | Slower lookup |
| array | Predictability | No resizing |

---

### Interview-Oriented Explanation

A strong interview answer explains:
- Available options
- Pros and cons
- Reason for the final choice

Example:
> "I chose unordered_map for faster lookup, trading ordering and higher memory usage for performance, which suits a low-latency requirement."

---

### One-Line Summary

STL trade-offs are the deliberate compromises between performance, memory usage, predictability, and flexibility when selecting containers or algorithms in C++.
