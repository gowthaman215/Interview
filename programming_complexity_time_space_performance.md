= Programming Complexity & Performance — Complete Reference with Examples
:toc:
:toclevels: 4
:sectnums:
:source-highlighter: coderay

== 1. Introduction

Programming complexity describes **how a program’s resource usage grows** as input size increases.

Resources typically measured:
* Time (CPU operations)
* Space (memory usage)

Complexity helps us:
* Predict performance
* Compare algorithms
* Avoid slow or memory-heavy solutions
* Design scalable systems

---

== 2. Types of Complexity

There are three main types:

* Time Complexity
* Space (Memory) Complexity
* Performance Complexity (real-world behavior)

---

== 3. Time Complexity

=== What Is Time Complexity?

Time complexity measures:
> How the number of operations grows with input size `n`

It does NOT measure:
* Actual execution time
* CPU speed
* Compiler optimizations

Instead, it measures **growth rate**.

---

=== Big-O Notation

Big-O describes the **upper bound (worst-case)**.

Common Big-O values:

| Notation | Name | Example |
|--------|------|--------|
| O(1) | Constant | Array access |
| O(log n) | Logarithmic | Binary search |
| O(n) | Linear | Loop |
| O(n log n) | Linearithmic | Merge sort |
| O(n²) | Quadratic | Nested loops |
| O(2ⁿ) | Exponential | Brute force recursion |

---

== 4. Common Time Complexity Examples

=== O(1) — Constant Time

[source,cpp]
----
int x = arr[0];
----

Time does not depend on `n`.

---

=== O(n) — Linear Time

[source,cpp]
----
for (int i = 0; i < n; i++) {
    sum += arr[i];
}
----

Time grows linearly with input size.

---

=== O(n²) — Quadratic Time

[source,cpp]
----
for (int i = 0; i < n; i++) {
    for (int j = 0; j < n; j++) {
        count++;
    }
}
----

Very slow for large `n`.

---

=== O(log n) — Logarithmic Time

[source,cpp]
----
int binarySearch(vector<int>& v, int key) {
    int l = 0, r = v.size() - 1;
    while (l <= r) {
        int m = (l + r) / 2;
        if (v[m] == key) return m;
        if (v[m] < key) l = m + 1;
        else r = m - 1;
    }
    return -1;
}
----

Each step halves the input size.

---

== 5. Best, Average, and Worst Case

Algorithms can behave differently.

Example: Linear search

| Case | Complexity |
|----|-----------|
| Best | O(1) |
| Average | O(n) |
| Worst | O(n) |

Big-O usually refers to **worst case**.

---

== 6. Space (Memory) Complexity

=== What Is Space Complexity?

Space complexity measures:
> How memory usage grows with input size

Includes:
* Variables
* Data structures
* Recursion stack
* Heap allocations

---

=== O(1) — Constant Space

[source,cpp]
----
int sum = 0;
----

Memory does not grow with input.

---

=== O(n) — Linear Space

[source,cpp]
----
vector<int> v(n);
----

Memory grows with input size.

---

=== O(n) — Recursion Stack

[source,cpp]
----
int factorial(int n) {
    if (n == 0) return 1;
    return n * factorial(n - 1);
}
----

Uses O(n) stack space.

---

=== O(n²) — Matrix Allocation

[source,cpp]
----
vector<vector<int>> matrix(n, vector<int>(n));
----

Memory grows quadratically.

---

== 7. Time vs Space Trade-Off

Often, we trade memory for speed.

Example: Hash table vs linear search

| Approach | Time | Space |
|------|------|------|
| Linear search | O(n) | O(1) |
| Hash table | O(1) avg | O(n) |

---

== 8. Performance Complexity (Real-World)

=== Complexity vs Performance

Big-O does NOT capture:
* Cache behavior
* Branch prediction
* Memory latency
* Parallelism
* Constant factors

Example:

[source,cpp]
----
vector<int> v;
list<int> l;
----

Both traversal are O(n), but:
* `vector` is much faster (cache-friendly)
* `list` is slower (pointer chasing)

---

== 9. Constant Factors Matter

[source,cpp]
----
for (int i = 0; i < n; i++) { ... }
----

vs

[source,cpp]
----
for (int i = 0; i < n; i++) {
    expensive_operation();
}
----

Same Big-O, very different performance.

---

== 10. Measuring Performance in Practice

=== Time Measurement

[source,cpp]
----
#include <chrono>

auto start = std::chrono::high_resolution_clock::now();
// code
auto end = std::chrono::high_resolution_clock::now();
----

---

=== Memory Measurement

* Heap profiling tools
* Valgrind / Massif
* OS-level metrics

---

== 11. Amortized Complexity

Some operations are expensive occasionally.

Example: `std::vector::push_back`

| Operation | Cost |
|--------|------|
| Most inserts | O(1) |
| Reallocation | O(n) |
| Amortized | O(1) |

---

== 12. Algorithmic Complexity vs System Performance

| Aspect | Algorithm | System |
|-----|----------|-------|
| Focus | Growth rate | Real execution |
| Tools | Big-O | Profilers |
| Examples | sort vs search | cache misses |

Both must be considered.

---

== 13. Common Complexity Mistakes

* Assuming O(1) is always fast
* Ignoring memory access patterns
* Using recursion without stack awareness
* Confusing average and worst case
* Over-optimizing prematurely

---

== 14. Choosing the Right Complexity

General rules:
* Prefer O(n log n) or better
* Avoid O(n²) for large inputs
* Avoid exponential algorithms
* Measure before optimizing

---

== 15. Interview Perspective

What interviewers expect:
* Correct Big-O analysis
* Awareness of space usage
* Understanding of trade-offs
* Practical reasoning

---

== 16. Real-World Example

[source,cpp]
----
unordered_map<int,int> m;
----

Average lookup: O(1)  
Worst case: O(n)  
Memory cost: high

Used when speed matters more than memory.

---

== 17. Final Takeaway

* Time complexity measures growth of operations
* Space complexity measures growth of memory
* Performance depends on hardware, cache, and constants
* Big-O guides design, profiling confirms reality

Understanding complexity is essential for writing scalable and efficient software.

