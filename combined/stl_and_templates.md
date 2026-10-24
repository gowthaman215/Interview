# C++ Templates and STL

A single, de-duplicated reference merged from:
`cpp_templates_and_stl_complete_reference.md`, `cpp_stl_containers_complete_reference.md`,
`cpp_stl_algorithms_complete_reference.md` and `stl_tradeoffs.md`.

## Contents

1. [Introduction](#introduction)
2. [Templates — The Foundation of STL](#templates--the-foundation-of-stl)
3. [STL Containers](#stl-containers)
4. [Iterators](#iterators)
5. [STL Algorithms](#stl-algorithms)
6. [Other STL Building Blocks](#other-stl-building-blocks)
7. [Complexity Guarantees](#complexity-guarantees)
8. [STL Trade-offs](#stl-trade-offs)
9. [Guidelines and Common Mistakes](#guidelines-and-common-mistakes)
10. [Final Takeaway](#final-takeaway)

---

## Introduction

The C++ Standard Template Library (STL) is built entirely on **templates**.
To understand STL properly, you must first understand **template-based generic programming**.

STL consists of:
- Containers (data storage)
- Algorithms (data processing)
- Iterators (abstraction layer)
- Function objects
- Allocators (memory control)

All of these are template-based and zero-cost. Algorithms operate on **iterator ranges**, not containers.

---

## Templates — The Foundation of STL

Templates enable **generic programming**:
> Write code once, use it for any type.

STL would not exist without templates.

### Function Templates

```cpp
template<typename T>
T add(T a, T b) {
    return a + b;
}
```

The compiler generates functions as needed:

```cpp
add<int>(1, 2);
add(1.5, 2.5);   // type deduced
```

Multiple template parameters:

```cpp
template<typename T, typename U>
auto multiply(T a, U b) {
    return a * b;
}
```

### Class Templates

```cpp
template<typename T>
class Box {
    T value;
public:
    Box(T v) : value(v) {}
    T get() const { return value; }
};

Box<int> bi(10);
Box<std::string> bs("hello");
```

Every STL container is a **class template**.

### Template Specialization

Full specialization — when a specific type needs special handling:

```cpp
template<>
class Box<bool> {
public:
    Box(bool) {}
};
```

Partial specialization (class templates only) — used heavily by the STL:

```cpp
template<typename T>
class Box<T*> {
};
```

### Variadic Templates (C++11)

```cpp
template<typename... Args>
void log(Args... args) {}
```

Used in `std::tuple`, `std::function`, and `emplace`.

### Perfect Forwarding

```cpp
template<typename T>
void wrapper(T&& arg) {
    process(std::forward<T>(arg));
}
```

Preserves value category (lvalue → lvalue, rvalue → rvalue). Critical for STL efficiency.

---

## STL Containers

STL containers are **template-based data structures** that manage collections of objects.
All containers follow RAII and manage memory automatically.

Container categories:
- Sequence containers
- Associative containers (ordered)
- Unordered containers (hash-based)
- Container adapters

### Sequence Containers

Sequence containers store elements in a linear order.

#### std::vector

Dynamic array with contiguous memory.

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

- Contiguous memory, cache friendly
- O(1) fast random access
- Insertion in middle is expensive
- Iterators invalidated on reallocation

#### std::deque

Double-ended queue.

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

- Fast insertion at both ends
- Non-contiguous (segmented) memory
- Iterators stable unless reallocation of blocks

#### std::list

Doubly linked list.

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

- O(1) insertion anywhere
- No random access
- Poor cache locality
- Rarely needed

#### std::forward_list

Singly linked list.

```cpp
std::forward_list<int> fl;

fl.push_front(1);
fl.insert_after(fl.before_begin(), 2);
fl.erase_after(fl.before_begin());
```

- Minimal memory overhead
- No `size()`
- One-direction traversal

#### std::array

Fixed-size container.

```cpp
std::array<int, 3> a = {1,2,3};

a.size();
a.empty();

a[0];
a.at(1);
a.front();
a.back();
```

- Stack allocated
- Size known at compile time
- Zero overhead

### Associative Containers (Ordered)

Implemented using balanced trees (Red-Black Tree).

#### std::map

Key-value pairs, sorted by key.

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

- Keys are unique
- O(log n) operations
- Iterators stable

#### std::multimap

Allows duplicate keys.

```cpp
std::multimap<int, std::string> mm;
mm.insert({1, "one"});
mm.insert({1, "uno"});
```

#### std::set

Sorted unique keys.

```cpp
std::set<int> s;

s.insert(3);
s.emplace(1);

s.find(1);
s.count(3);

s.erase(3);
```

#### std::multiset

Allows duplicate keys.

```cpp
std::multiset<int> ms;
ms.insert(1);
ms.insert(1);
```

### Unordered Containers (Hash-Based)

Implemented using hash tables.

#### std::unordered_map

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

- O(1) average lookup
- No ordering
- Higher memory usage
- Iterators invalidated on rehash

#### std::unordered_multimap, std::unordered_set, std::unordered_multiset

```cpp
std::unordered_multimap<int, std::string> umm;   // duplicate keys

std::unordered_set<int> us;
us.insert(1);
us.find(1);
us.erase(1);

std::unordered_multiset<int> ums;
```

### Container Adapters

Adapters provide restricted interfaces.

#### std::stack

LIFO container.

```cpp
std::stack<int> st;

st.push(1);
st.top();
st.pop();
st.empty();
```

#### std::queue

FIFO container.

```cpp
std::queue<int> q;

q.push(1);
q.front();
q.back();
q.pop();
```

#### std::priority_queue

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

### Common Container Member Functions

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

## Iterators

Iterators are generalized pointers — the glue between containers and algorithms. Algorithms only see iterators.

```cpp
for(auto it = v.begin(); it != v.end(); ++it) {
    std::cout << *it;
}
```

### Iterator Categories

| Category | Capability |
|-------|-----------|
| Input | Read once |
| Output | Write once |
| Forward | Multi-pass |
| Bidirectional | ++, -- |
| Random Access | +, -, [] |

### Iterator Invalidation

```cpp
std::vector<int> v = {1,2};
auto it = v.begin();
v.push_back(3);   // it may be invalid
```

| Container | Invalidation |
|--------|-------------|
| vector | On reallocation |
| deque | On insertion at ends |
| list | Never (except erased) |
| map | Never (except erased) |
| unordered_map | On rehash |

---

## STL Algorithms

STL algorithms are **generic functions** that operate on **iterator ranges**.

Key principles:
- Algorithms do NOT own data
- They work on iterators
- Containers and algorithms are decoupled — algorithms do NOT know container types
- Performance guarantees are well-defined

Most algorithms are declared in:
```cpp
#include <algorithm>
#include <numeric>
```

### Iterator-Based Design

All algorithms operate on:
```cpp
[first, last)
```

Where `first` is inclusive and `last` is exclusive.

```cpp
std::sort(v.begin(), v.end());
```

### Non-Modifying Sequence Algorithms

These algorithms **do not change elements**.

```cpp
auto it = std::find(v.begin(), v.end(), 5);   // first occurrence
std::count(v.begin(), v.end(), 1);
std::count_if(v.begin(), v.end(),
              [](int x){ return x > 10; });
std::find_if(v.begin(), v.end(), pred);       // also find_if_not
std::all_of(v.begin(), v.end(), pred);
std::any_of(v.begin(), v.end(), pred);
std::none_of(v.begin(), v.end(), pred);
```

### Modifying Sequence Algorithms

These algorithms **modify elements**.

```cpp
std::copy(src.begin(), src.end(), dest.begin());
std::move(src.begin(), src.end(), dest.begin());   // moves elements
std::transform(v.begin(), v.end(), v.begin(),
               [](int x){ return x * 2; });
std::replace(v.begin(), v.end(), 1, 100);          // also replace_if
std::fill(v.begin(), v.end(), 0);
```

#### std::remove / remove_if (Important)

```cpp
auto it = std::remove(v.begin(), v.end(), 3);
v.erase(it, v.end());
```

- Does NOT erase from container
- Shifts elements (use the erase-remove idiom)

### Partitioning Algorithms

```cpp
std::partition(v.begin(), v.end(),
               [](int x){ return x % 2 == 0; });
```

`std::stable_partition` preserves order.

### Sorting Algorithms

```cpp
std::sort(v.begin(), v.end());                       // avg O(n log n), random-access iterators
std::partial_sort(v.begin(), v.begin()+3, v.end());
std::nth_element(v.begin(), v.begin()+k, v.end());   // places nth element correctly
```

`std::stable_sort` preserves relative order.

### Binary Search Algorithms (Sorted Range)

```cpp
std::binary_search(v.begin(), v.end(), 10);
auto it1 = std::lower_bound(v.begin(), v.end(), 10);
auto it2 = std::upper_bound(v.begin(), v.end(), 10);
auto range = std::equal_range(v.begin(), v.end(), 10);
```

### Set Algorithms (Sorted Ranges)

```cpp
std::set_union(a.begin(), a.end(),
               b.begin(), b.end(),
               out.begin());
```

Also: `std::set_intersection`, `std::set_difference`, `std::set_symmetric_difference`.

### Heap Algorithms

```cpp
std::make_heap(v.begin(), v.end());

v.push_back(10);
std::push_heap(v.begin(), v.end());

std::pop_heap(v.begin(), v.end());
v.pop_back();

std::sort_heap(v.begin(), v.end());
```

### Permutation Algorithms

```cpp
std::next_permutation(v.begin(), v.end());
```

Also: `std::prev_permutation`.

### Numeric Algorithms (`<numeric>`)

```cpp
int sum = std::accumulate(v.begin(), v.end(), 0);
std::inner_product(a.begin(), a.end(), b.begin(), 0);
std::partial_sum(v.begin(), v.end(), v.begin());
```

Also: `std::adjacent_difference`.

### Min / Max Algorithms

```cpp
std::min(a, b);
std::max(a, b);
auto it = std::max_element(v.begin(), v.end());   // also min_element
```

Also: `std::minmax`, `std::minmax_element`.

### Comparison Algorithms

```cpp
std::equal(a.begin(), a.end(), b.begin());
std::lexicographical_compare(a.begin(), a.end(),
                             b.begin(), b.end());
```

### Search Algorithms

```cpp
std::search(v.begin(), v.end(),
            sub.begin(), sub.end());
```

Also: `std::find_end`, `std::find_first_of`.

### Algorithms vs Member Functions

Prefer:
```cpp
std::find(v.begin(), v.end(), x);
```

Over:
```cpp
container.find(x);  // only for associative containers
```

Algorithms are more generic.

### Algorithms + Lambdas (Modern C++)

```cpp
std::count_if(v.begin(), v.end(),
              [](int x){ return x > 5; });
```

Lambdas replaced many functors.

### When to Use Which Algorithm

- Need search → find / binary_search
- Need reorder → sort / partition
- Need transform → transform
- Need aggregate → accumulate
- Need top-k → nth_element

---

## Other STL Building Blocks

### Function Objects (Functors)

```cpp
struct Greater {
    bool operator()(int a, int b) const {
        return a > b;
    }
};
```

Used by algorithms and containers.

### Emplace vs Insert (C++11)

```cpp
v.emplace_back(10);  // constructs in place
v.push_back(10);     // may create temporary
```

Emplace avoids unnecessary copies.

### Allocators (Advanced)

Allocators control memory behavior.

```cpp
std::vector<int, MyAllocator<int>> v;
```

Used in custom memory pools, low-latency systems, and embedded systems.

### STL + RAII

```cpp
{
    std::vector<int> v = {1,2,3};
} // memory released automatically
```

STL containers fully follow RAII.

### STL in Modern C++11/14

- Move semantics reduce copies
- Lambdas replace functors
- auto simplifies code
- make_unique added
- constexpr containers (limited)

---

## Complexity Guarantees

STL guarantees performance, and design depends on these guarantees. Always check complexity.

- vector::push_back → amortized O(1)
- map::find → O(log n)
- unordered_map::find → O(1) average
- find → O(n)
- sort → O(n log n)
- binary_search → O(log n)
- remove → O(n)

---

## STL Trade-offs

STL trade-offs are the conscious decisions made when choosing STL containers, algorithms, and utilities by balancing:

- Performance
- Memory usage
- Predictability
- Safety
- Flexibility

No STL container or algorithm is universally best; each choice involves compromises. STL forces conscious design.

### vector vs list

| Aspect | vector | list |
|------|--------|------|
| Memory layout | Contiguous | Non-contiguous |
| Cache friendliness | Excellent | Poor |
| Random access | O(1) | Not supported |
| Insert/Delete (middle) | O(n) | O(1) |
| Memory overhead | Low | High |

`vector` offers speed and cache locality; `list` offers efficient insertions/deletions.
Preferred choice in most systems: `vector`.

### vector vs deque

| Aspect | vector | deque |
|------|--------|-------|
| Memory layout | Contiguous | Segmented |
| Push front | Slow | Fast |
| Push back | Fast | Fast |
| Random access | O(1) | O(1) |
| Cache locality | Best | Good |

`deque` supports efficient insertion at both ends; `vector` provides better cache performance.

### map vs unordered_map

| Aspect | map | unordered_map |
|------|-----|---------------|
| Ordering | Sorted | Unordered |
| Lookup complexity | O(log n) | O(1) average |
| Worst-case | Predictable | Can degrade |
| Memory usage | Lower | Higher |
| Cache friendliness | Poor | Better |

`map` provides ordering and predictable performance; `unordered_map` provides faster average lookup at the cost of memory.

### set vs unordered_set

| Aspect | set | unordered_set |
|------|-----|---------------|
| Ordering | Sorted | Unordered |
| Lookup complexity | O(log n) | O(1) average |
| Memory usage | Lower | Higher |
| Use case | Ordered unique elements | Fast membership checks |

The trade-off mirrors `map` vs `unordered_map`.

### array vs vector

| Aspect | array | vector |
|------|-------|--------|
| Size | Fixed | Dynamic |
| Allocation | Stack | Heap |
| Overhead | None | Small |
| Resize capability | No | Yes |

`array` offers predictability and zero overhead; `vector` offers flexibility and dynamic resizing.
Low-latency systems prefer `array` where size is known.

### Algorithms vs Custom Loops

```cpp
std::sort(v.begin(), v.end());
```

vs a custom hand-written loop:
- STL algorithms are safer and more readable
- Custom loops allow fine-grained micro-optimizations

Best practice: use STL first, optimize only if required.

### Iterators vs Indexing

```cpp
for (auto it = v.begin(); it != v.end(); ++it)
```

vs

```cpp
for (size_t i = 0; i < v.size(); ++i)
```

- Iterators are generic and container-independent
- Indexing can be simpler and sometimes faster

### Smart Pointers in STL Containers

```cpp
std::vector<std::unique_ptr<Foo>>
```

- Ensures clear ownership and safety
- Introduces slight indirection cost

Modern C++ favors ownership clarity over raw pointers.

### Memory vs Speed (Summary)

| Choice | Gain | Cost |
|------|------|------|
| vector | Cache speed | Resize cost / slow inserts |
| list | Fast insertion | Cache misses |
| unordered_map | Fast lookup | Memory usage |
| map | Ordering | Slower lookup |
| array | Predictability | No resizing |

### Interview-Oriented Explanation

A strong interview answer explains:
- Available options
- Pros and cons
- Reason for the final choice

Example:
> "I chose unordered_map for faster lookup, trading ordering and higher memory usage for performance, which suits a low-latency requirement."

---

## Guidelines and Common Mistakes

### Container Selection and Usage Guidelines

- Default → vector; reserve capacity early
- Need ordering → map / set
- Need fast lookup → unordered_map
- Frequent insert anywhere → list (avoid list unless necessary)
- Fixed size → array
- Use algorithms instead of loops
- Understand memory layout
- Measure performance

### Common Mistakes

- Using list without reason
- Not reserving vector capacity
- Copying large containers
- Ignoring iterator invalidation
- Overusing unordered containers / using them without hash tuning
- Forgetting the erase-remove idiom; assuming remove deletes elements
- Using sort on non-random-access iterators
- Binary search on unsorted data

---

## Final Takeaway

STL is not just a library.
It is a **generic programming framework** built on templates, iterators, and strict performance guarantees.
Its algorithms separate **what** you do from **how** data is stored, and choosing the correct container and member functions is critical for performance and correctness.

Mastery requires understanding:
- Templates
- Ownership
- Iterators
- Complexity
- Trade-offs
