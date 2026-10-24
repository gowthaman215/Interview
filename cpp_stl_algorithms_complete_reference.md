= C++ STL Algorithms — Complete Reference with Examples
:toc:
:toclevels: 4
:sectnums:
:source-highlighter: coderay

== 1. Introduction to STL Algorithms

STL algorithms are **generic functions** that operate on **iterator ranges**.

Key principles:
* Algorithms do NOT own data
* They work on iterators
* Containers and algorithms are decoupled
* Performance guarantees are well-defined

Most algorithms are declared in:
[source,cpp]
----
#include <algorithm>
#include <numeric>
----

---

== 2. Iterator-Based Design

All algorithms operate on:
[source,cpp]
----
[first, last)
----

Where:
* `first` → inclusive
* `last` → exclusive

[source,cpp]
----
std::sort(v.begin(), v.end());
----

---

== 3. Non-Modifying Sequence Algorithms

These algorithms **do not change elements**.

---

=== std::find

[source,cpp]
----
auto it = std::find(v.begin(), v.end(), 5);
----

Finds first occurrence.

---

=== std::count

[source,cpp]
----
std::count(v.begin(), v.end(), 1);
----

---

=== std::count_if

[source,cpp]
----
std::count_if(v.begin(), v.end(),
              [](int x){ return x > 10; });
----

---

=== std::find_if / find_if_not

[source,cpp]
----
std::find_if(v.begin(), v.end(), pred);
----

---

=== std::all_of / any_of / none_of

[source,cpp]
----
std::all_of(v.begin(), v.end(), pred);
std::any_of(v.begin(), v.end(), pred);
std::none_of(v.begin(), v.end(), pred);
----

---

== 4. Modifying Sequence Algorithms

These algorithms **modify elements**.

---

=== std::copy

[source,cpp]
----
std::copy(src.begin(), src.end(), dest.begin());
----

---

=== std::move

[source,cpp]
----
std::move(src.begin(), src.end(), dest.begin());
----

Moves elements.

---

=== std::transform

[source,cpp]
----
std::transform(v.begin(), v.end(), v.begin(),
               [](int x){ return x * 2; });
----

---

=== std::replace / replace_if

[source,cpp]
----
std::replace(v.begin(), v.end(), 1, 100);
----

---

=== std::fill

[source,cpp]
----
std::fill(v.begin(), v.end(), 0);
----

---

=== std::remove / remove_if (IMPORTANT)

[source,cpp]
----
auto it = std::remove(v.begin(), v.end(), 3);
v.erase(it, v.end());
----

* Does NOT erase from container
* Shifts elements

---

== 5. Partitioning Algorithms

---

=== std::partition

[source,cpp]
----
std::partition(v.begin(), v.end(),
               [](int x){ return x % 2 == 0; });
----

---

=== std::stable_partition

Preserves order.

---

== 6. Sorting Algorithms

---

=== std::sort

[source,cpp]
----
std::sort(v.begin(), v.end());
----

* Average O(n log n)
* Requires random-access iterators

---

=== std::stable_sort

Preserves relative order.

---

=== std::partial_sort

[source,cpp]
----
std::partial_sort(v.begin(), v.begin()+3, v.end());
----

---

=== std::nth_element

[source,cpp]
----
std::nth_element(v.begin(), v.begin()+k, v.end());
----

Places nth element correctly.

---

== 7. Binary Search Algorithms (Sorted Range)

---

=== std::binary_search

[source,cpp]
----
std::binary_search(v.begin(), v.end(), 10);
----

---

=== std::lower_bound / upper_bound

[source,cpp]
----
auto it1 = std::lower_bound(v.begin(), v.end(), 10);
auto it2 = std::upper_bound(v.begin(), v.end(), 10);
----

---

=== std::equal_range

[source,cpp]
----
auto range = std::equal_range(v.begin(), v.end(), 10);
----

---

== 8. Set Algorithms (Sorted Ranges)

---

=== std::set_union

[source,cpp]
----
std::set_union(a.begin(), a.end(),
               b.begin(), b.end(),
               out.begin());
----

---

=== std::set_intersection

---

=== std::set_difference

---

=== std::set_symmetric_difference

---

== 9. Heap Algorithms

---

=== std::make_heap

[source,cpp]
----
std::make_heap(v.begin(), v.end());
----

---

=== std::push_heap

[source,cpp]
----
v.push_back(10);
std::push_heap(v.begin(), v.end());
----

---

=== std::pop_heap

[source,cpp]
----
std::pop_heap(v.begin(), v.end());
v.pop_back();
----

---

=== std::sort_heap

[source,cpp]
----
std::sort_heap(v.begin(), v.end());
----

---

== 10. Permutation Algorithms

---

=== std::next_permutation

[source,cpp]
----
std::next_permutation(v.begin(), v.end());
----

---

=== std::prev_permutation

---

== 11. Numeric Algorithms (<numeric>)

---

=== std::accumulate

[source,cpp]
----
int sum = std::accumulate(v.begin(), v.end(), 0);
----

---

=== std::inner_product

[source,cpp]
----
std::inner_product(a.begin(), a.end(), b.begin(), 0);
----

---

=== std::partial_sum

[source,cpp]
----
std::partial_sum(v.begin(), v.end(), v.begin());
----

---

=== std::adjacent_difference

---

== 12. Min / Max Algorithms

---

=== std::min / max

[source,cpp]
----
std::min(a, b);
std::max(a, b);
----

---

=== std::min_element / max_element

[source,cpp]
----
auto it = std::max_element(v.begin(), v.end());
----

---

=== std::minmax / minmax_element

---

== 13. Comparison Algorithms

---

=== std::equal

[source,cpp]
----
std::equal(a.begin(), a.end(), b.begin());
----

---

=== std::lexicographical_compare

[source,cpp]
----
std::lexicographical_compare(a.begin(), a.end(),
                             b.begin(), b.end());
----

---

== 14. Search Algorithms

---

=== std::search

[source,cpp]
----
std::search(v.begin(), v.end(),
            sub.begin(), sub.end());
----

---

=== std::find_end

---

=== std::find_first_of

---

== 15. Algorithms vs Member Functions

Prefer:
[source,cpp]
----
std::find(v.begin(), v.end(), x);
----

Over:
[source,cpp]
----
container.find(x);  // only for associative containers
----

Algorithms are more generic.

---

== 16. Algorithm Complexity Guarantees

Examples:
* find → O(n)
* sort → O(n log n)
* binary_search → O(log n)
* remove → O(n)

Always check complexity.

---

== 17. Common Algorithm Mistakes

* Forgetting erase-remove idiom
* Using sort on non-random iterators
* Binary search on unsorted data
* Assuming remove deletes elements
* Ignoring iterator invalidation

---

== 18. Algorithms + Lambdas (Modern C++)

[source,cpp]
----
std::count_if(v.begin(), v.end(),
              [](int x){ return x > 5; });
----

Lambdas replaced many functors.

---

== 19. When to Use Which Algorithm

* Need search → find / binary_search
* Need reorder → sort / partition
* Need transform → transform
* Need aggregate → accumulate
* Need top-k → nth_element

---

== 20. Final Takeaway

STL algorithms provide powerful, efficient, and reusable operations that separate *what* you do from *how* data is stored.

Mastering algorithms is essential for writing modern, clean, and high-performance C++ code.

