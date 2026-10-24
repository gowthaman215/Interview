= C++ STL Containers — Complete Reference with Member Function Examples
:toc:
:toclevels: 4
:sectnums:
:source-highlighter: coderay

== 1. STL Containers Overview

STL containers are **template-based data structures** that manage collections of objects.

Container categories:
* Sequence containers
* Associative containers (ordered)
* Unordered containers (hash-based)
* Container adapters

All containers follow RAII and manage memory automatically.

---

== 2. Sequence Containers

Sequence containers store elements in a linear order.

---

=== 2.1 std::vector

Dynamic array with contiguous memory.

==== Common Member Functions

[source,cpp]
----
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
----

Key notes:
* Fast random access
* Insertion in middle is expensive
* Iterators invalidated on reallocation

---

=== 2.2 std::deque

Double-ended queue.

==== Common Member Functions

[source,cpp]
----
std::deque<int> d;

d.push_back(1);
d.push_front(2);
d.pop_back();
d.pop_front();

d.front();
d.back();

d.size();
d.clear();
----

Key notes:
* Fast insertion at both ends
* No contiguous memory
* Iterators stable unless reallocation of blocks

---

=== 2.3 std::list

Doubly linked list.

==== Common Member Functions

[source,cpp]
----
std::list<int> l;

l.push_back(1);
l.push_front(2);

l.insert(l.begin(), 5);
l.erase(l.begin());

l.remove(2);
l.sort();
l.reverse();
l.unique();
----

Key notes:
* Fast insertion anywhere
* No random access
* Poor cache locality

---

=== 2.4 std::forward_list

Singly linked list.

==== Common Member Functions

[source,cpp]
----
std::forward_list<int> fl;

fl.push_front(1);
fl.insert_after(fl.before_begin(), 2);
fl.erase_after(fl.before_begin());
----

Key notes:
* Minimal memory overhead
* No size()
* One-direction traversal

---

=== 2.5 std::array

Fixed-size container.

==== Common Member Functions

[source,cpp]
----
std::array<int, 3> a = {1,2,3};

a.size();
a.empty();

a[0];
a.at(1);
a.front();
a.back();
----

Key notes:
* Stack allocated
* Size known at compile time
* Zero overhead

---

== 3. Associative Containers (Ordered)

Implemented using balanced trees (Red-Black Tree).

---

=== 3.1 std::map

Key-value pairs, sorted by key.

==== Common Member Functions

[source,cpp]
----
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
----

Key notes:
* Keys are unique
* O(log n) operations
* Iterators stable

---

=== 3.2 std::multimap

Allows duplicate keys.

[source,cpp]
----
std::multimap<int, std::string> mm;
mm.insert({1, "one"});
mm.insert({1, "uno"});
----

---

=== 3.3 std::set

Sorted unique keys.

==== Common Member Functions

[source,cpp]
----
std::set<int> s;

s.insert(3);
s.emplace(1);

s.find(1);
s.count(3);

s.erase(3);
----

---

=== 3.4 std::multiset

Allows duplicate keys.

[source,cpp]
----
std::multiset<int> ms;
ms.insert(1);
ms.insert(1);
----

---

== 4. Unordered Containers (Hash-Based)

Implemented using hash tables.

---

=== 4.1 std::unordered_map

==== Common Member Functions

[source,cpp]
----
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
----

Key notes:
* O(1) average lookup
* No ordering
* Iterators invalidated on rehash

---

=== 4.2 std::unordered_multimap

Allows duplicate keys.

[source,cpp]
----
std::unordered_multimap<int, std::string> umm;
----

---

=== 4.3 std::unordered_set

[source,cpp]
----
std::unordered_set<int> us;

us.insert(1);
us.find(1);
us.erase(1);
----

---

=== 4.4 std::unordered_multiset

[source,cpp]
----
std::unordered_multiset<int> ums;
----

---

== 5. Container Adapters

Adapters provide restricted interfaces.

---

=== 5.1 std::stack

LIFO container.

[source,cpp]
----
std::stack<int> st;

st.push(1);
st.top();
st.pop();
st.empty();
----

---

=== 5.2 std::queue

FIFO container.

[source,cpp]
----
std::queue<int> q;

q.push(1);
q.front();
q.back();
q.pop();
----

---

=== 5.3 std::priority_queue

Heap-based queue.

[source,cpp]
----
std::priority_queue<int> pq;

pq.push(10);
pq.push(5);
pq.top();  // largest
pq.pop();
----

Min-heap:

[source,cpp]
----
std::priority_queue<int, std::vector<int>, std::greater<int>> minpq;
----

---

== 6. Common Container Member Functions

Most containers support:

[source,cpp]
----
size();
empty();
clear();
begin();
end();
cbegin();
cend();
----

---

== 7. Iterator Invalidation Summary

| Container | Invalidation |
|--------|-------------|
| vector | On reallocation |
| deque | On insertion at ends |
| list | Never (except erased) |
| map | Never (except erased) |
| unordered_map | On rehash |

---

== 8. Container Selection Guidelines

* Default → vector
* Need ordering → map / set
* Need fast lookup → unordered_map
* Frequent insert anywhere → list
* Fixed size → array

---

== 9. Common Mistakes

* Using list without need
* Forgetting reserve() for vector
* Ignoring iterator invalidation
* Using unordered containers without hash tuning

---

== 10. Final Takeaway

STL containers provide safe, efficient, and well-defined data structures.
Choosing the correct container and using the right member functions is critical for performance and correctness in modern C++.

