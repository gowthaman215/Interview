= STL Trade-offs in C++
:toc:
:toclevels: 3
:sectnums:
:source-highlighter: coderay

== Introduction

STL trade-offs refer to the conscious decisions made when choosing STL containers, algorithms, and utilities by balancing:

* Performance
* Memory usage
* Predictability
* Safety
* Flexibility

No STL container or algorithm is universally best; each choice involves compromises.

---

== vector vs list

| Aspect | vector | list |
|------|--------|------|
| Memory layout | Contiguous | Non-contiguous |
| Cache friendliness | Excellent | Poor |
| Random access | O(1) | Not supported |
| Insert/Delete (middle) | O(n) | O(1) |
| Memory overhead | Low | High |

Trade-off:
* `vector` offers speed and cache locality
* `list` offers efficient insertions/deletions

Preferred choice in most systems: `vector`

---

== vector vs deque

| Aspect | vector | deque |
|------|--------|-------|
| Memory layout | Contiguous | Segmented |
| Push front | Slow | Fast |
| Push back | Fast | Fast |
| Random access | O(1) | O(1) |
| Cache locality | Best | Good |

Trade-off:
* `deque` supports efficient insertion at both ends
* `vector` provides better cache performance

---

== map vs unordered_map

| Aspect | map | unordered_map |
|------|-----|---------------|
| Ordering | Sorted | Unordered |
| Lookup complexity | O(log n) | O(1) average |
| Worst-case | Predictable | Can degrade |
| Memory usage | Lower | Higher |
| Cache friendliness | Poor | Better |

Trade-off:
* `map` provides ordering and predictable performance
* `unordered_map` provides faster average lookup at the cost of memory

---

== set vs unordered_set

| Aspect | set | unordered_set |
|------|-----|---------------|
| Ordering | Sorted | Unordered |
| Lookup complexity | O(log n) | O(1) average |
| Memory usage | Lower | Higher |
| Use case | Ordered unique elements | Fast membership checks |

Trade-off mirrors `map` vs `unordered_map`.

---

== array vs vector

| Aspect | array | vector |
|------|-------|--------|
| Size | Fixed | Dynamic |
| Allocation | Stack | Heap |
| Overhead | None | Small |
| Resize capability | No | Yes |

Trade-off:
* `array` offers predictability and zero overhead
* `vector` offers flexibility and dynamic resizing

Low-latency systems prefer `array` where size is known.

---

== Algorithms vs Custom Loops

[source,cpp]
----
std::sort(v.begin(), v.end());
----

vs

[source,cpp]
----
// Custom hand-written loop
----

Trade-off:
* STL algorithms are safer and more readable
* Custom loops allow fine-grained micro-optimizations

Best practice: use STL first, optimize only if required.

---

== Iterators vs Indexing

[source,cpp]
----
for (auto it = v.begin(); it != v.end(); ++it)
----

vs

[source,cpp]
----
for (size_t i = 0; i < v.size(); ++i)
----

Trade-off:
* Iterators are generic and container-independent
* Indexing can be simpler and sometimes faster

---

== Smart Pointers in STL Containers

[source,cpp]
----
std::vector<std::unique_ptr<Foo>>
----

Trade-off:
* Ensures clear ownership and safety
* Introduces slight indirection cost

Modern C++ favors ownership clarity over raw pointers.

---

== Memory vs Speed (Summary)

| Choice | Gain | Cost |
|------|------|------|
| vector | Cache speed | Resize cost |
| list | Fast insertion | Cache misses |
| unordered_map | Fast lookup | Memory usage |
| map | Ordering | Slower lookup |
| array | Predictability | No resizing |

---

== Interview-Oriented Explanation

A strong interview answer explains:
* Available options
* Pros and cons
* Reason for the final choice

Example:
> "I chose unordered_map for faster lookup, trading ordering and higher memory usage for performance, which suits a low-latency requirement."

---

== One-Line Summary

STL trade-offs are the deliberate compromises between performance, memory usage, predictability, and flexibility when selecting containers or algorithms in C++.

