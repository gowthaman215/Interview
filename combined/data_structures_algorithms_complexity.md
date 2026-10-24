# Data Structures, Algorithms and Complexity

This file combines the following references into one document:

1. [Data Structures and Algorithms — Complete Reference with C++ Examples](#part-1) — from `data_structures_and_algorithms_complete_reference.md`
2. [Programming Complexity & Performance — Complete Reference with Examples](#part-2) — from `programming_complexity_time_space_performance.md`

---

<a id="part-1"></a>

*Part 1 — source: `data_structures_and_algorithms_complete_reference.md`*

## Data Structures and Algorithms — Complete Reference with C++ Examples

### 1. Introduction

Data Structures define **how data is stored**.
Algorithms define **how data is processed**.

Good software performance depends on:
- Correct data structure choice
- Efficient algorithm selection
- Understanding time and space complexity

---

### 2. Complexity Analysis (Big-O)

| Notation | Meaning |
|--------|--------|
| O(1) | Constant |
| O(log n) | Logarithmic |
| O(n) | Linear |
| O(n log n) | Linearithmic |
| O(n²) | Quadratic |

```cpp
for(int i=0;i<n;i++) {}   // O(n)
```

---

## PART I — DATA STRUCTURES

### 3. Arrays

#### Description
- Fixed-size
- Contiguous memory
- Fast access

```cpp
int arr[5] = {1,2,3,4,5};
```

Access: O(1)
Insert/Delete: O(n)

---

### 4. Dynamic Array (Vector)

```cpp
#include <vector>
std::vector<int> v = {1,2,3};
v.push_back(4);
```

- Dynamic resizing
- Amortized O(1) insert at end

---

### 5. Linked List

#### Singly Linked List

```cpp
struct Node {
    int data;
    Node* next;
};
```

Insert/Delete: O(1)
Search: O(n)

---

### 6. Stack (LIFO)

```cpp
#include <stack>
std::stack<int> st;
st.push(1);
st.pop();
```

Operations: push, pop, top
All O(1)

---

### 7. Queue (FIFO)

```cpp
#include <queue>
std::queue<int> q;
q.push(1);
q.pop();
```

Used in:
- Scheduling
- BFS

---

### 8. Deque

```cpp
#include <deque>
std::deque<int> d;
d.push_front(1);
d.push_back(2);
```

Insert/delete both ends.

---

### 9. Hash Table

```cpp
#include <unordered_map>
std::unordered_map<int,int> um;
um[1] = 100;
```

Average lookup: O(1)
Worst: O(n)

---

### 10. Tree

#### Binary Tree Node

```cpp
struct Node {
    int data;
    Node* left;
    Node* right;
};
```

---

### 11. Binary Search Tree (BST)

```cpp
bool search(Node* root, int key) {
    if(!root) return false;
    if(root->data == key) return true;
    if(key < root->data)
        return search(root->left, key);
    return search(root->right, key);
}
```

Average: O(log n)
Worst: O(n)

---

### 12. Heap

```cpp
#include <queue>
std::priority_queue<int> pq;
pq.push(10);
pq.top();
```

Used in:
- Scheduling
- Dijkstra
- Top-K problems

---

### 13. Graph

#### Representation (Adjacency List)

```cpp
#include <vector>
std::vector<std::vector<int>> graph(n);
graph[u].push_back(v);
```

---

### 14. Trie

Used for:
- Prefix search
- Autocomplete

```cpp
struct Trie {
    bool end;
    Trie* next[26];
};
```

---

### 15. Disjoint Set (Union-Find)

```cpp
class DSU {
    std::vector<int> parent;
public:
    DSU(int n): parent(n) {
        for(int i=0;i<n;i++) parent[i]=i;
    }
    int find(int x){
        if(parent[x]==x) return x;
        return parent[x]=find(parent[x]);
    }
    void unite(int a,int b){
        parent[find(a)] = find(b);
    }
};
```

Used in:
- Kruskal algorithm
- Connectivity problems

---

## PART II — ALGORITHMS

### 16. Sorting Algorithms

#### Bubble Sort (O(n²))

```cpp
void bubble(int a[], int n){
    for(int i=0;i<n;i++)
        for(int j=0;j<n-i-1;j++)
            if(a[j]>a[j+1])
                std::swap(a[j],a[j+1]);
}
```

---

#### Quick Sort (O(n log n) avg)

```cpp
void quickSort(int a[], int l, int r){
    if(l>=r) return;
    int p = a[(l+r)/2];
    int i=l,j=r;
    while(i<=j){
        while(a[i]<p) i++;
        while(a[j]>p) j--;
        if(i<=j) std::swap(a[i++],a[j--]);
    }
    quickSort(a,l,j);
    quickSort(a,i,r);
}
```

---

#### Merge Sort

```cpp
void mergeSort(vector<int>& v){
    if(v.size()<2) return;
}
```

Stable, O(n log n)

---

### 17. Searching Algorithms

#### Linear Search

```cpp
for(int x: v)
    if(x==key) return true;
```

---

#### Binary Search

```cpp
bool binarySearch(vector<int>& v,int k){
    int l=0,r=v.size()-1;
    while(l<=r){
        int m=(l+r)/2;
        if(v[m]==k) return true;
        if(v[m]<k) l=m+1;
        else r=m-1;
    }
    return false;
}
```

---

### 18. Graph Algorithms

#### BFS

```cpp
void bfs(int s){
    queue<int> q;
    q.push(s);
}
```

Used for:
- Shortest path (unweighted)
- Level order traversal

---

#### DFS

```cpp
void dfs(int u){
    visited[u]=true;
    for(int v: graph[u])
        if(!visited[v]) dfs(v);
}
```

---

### 19. Shortest Path Algorithms

#### Dijkstra

```cpp
priority_queue<pair<int,int>> pq;
```

Used for:
- Weighted graphs (non-negative)

---

#### Bellman-Ford

Handles negative edges.

---

### 20. Minimum Spanning Tree

#### Kruskal

```cpp
sort(edges.begin(),edges.end());
```

Uses:
- Disjoint Set

---

#### Prim

Uses:
- Priority Queue

---

### 21. Dynamic Programming (DP)

#### Fibonacci

```cpp
int fib(int n){
    if(n<=1) return n;
    return fib(n-1)+fib(n-2);
}
```

Optimized with memoization.

---

### 22. Greedy Algorithms

Example:
- Activity selection
- Coin change (specific cases)

---

### 23. Backtracking

Example:
- N-Queens
- Sudoku

```cpp
void solve(){
    if(valid) solve();
}
```

---

### 24. Famous Algorithms (Most Used)

- Binary Search
- Quick Sort
- Merge Sort
- BFS / DFS
- Dijkstra
- Kruskal
- Union-Find
- KMP
- Sliding Window
- Two Pointers

---

### 25. Choosing Data Structure & Algorithm

| Problem | Best Choice |
|------|-------------|
| Fast lookup | Hash table |
| Sorted data | BST / heap |
| Shortest path | Dijkstra |
| Connectivity | DSU |
| Prefix match | Trie |

---

### 26. Common Mistakes

- Using O(n²) unintentionally
- Wrong data structure
- Ignoring memory
- Overengineering

---

### 27. Final Takeaway

Data structures and algorithms form the **foundation of efficient software**.

Mastery requires:
- Understanding trade-offs
- Practicing implementations
- Applying correct patterns

This document is a **complete lifelong reference**.

---

<a id="part-2"></a>

*Part 2 — source: `programming_complexity_time_space_performance.md`*

## Programming Complexity & Performance — Complete Reference with Examples

### 1. Introduction

Programming complexity describes **how a program’s resource usage grows** as input size increases.

Resources typically measured:
- Time (CPU operations)
- Space (memory usage)

Complexity helps us:
- Predict performance
- Compare algorithms
- Avoid slow or memory-heavy solutions
- Design scalable systems

---

### 2. Types of Complexity

There are three main types:

- Time Complexity
- Space (Memory) Complexity
- Performance Complexity (real-world behavior)

---

### 3. Time Complexity

#### What Is Time Complexity?

Time complexity measures:
> How the number of operations grows with input size `n`

It does NOT measure:
- Actual execution time
- CPU speed
- Compiler optimizations

Instead, it measures **growth rate**.

---

#### Big-O Notation

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

### 4. Common Time Complexity Examples

#### O(1) — Constant Time

```cpp
int x = arr[0];
```

Time does not depend on `n`.

---

#### O(n) — Linear Time

```cpp
for (int i = 0; i < n; i++) {
    sum += arr[i];
}
```

Time grows linearly with input size.

---

#### O(n²) — Quadratic Time

```cpp
for (int i = 0; i < n; i++) {
    for (int j = 0; j < n; j++) {
        count++;
    }
}
```

Very slow for large `n`.

---

#### O(log n) — Logarithmic Time

```cpp
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
```

Each step halves the input size.

---

### 5. Best, Average, and Worst Case

Algorithms can behave differently.

Example: Linear search

| Case | Complexity |
|----|-----------|
| Best | O(1) |
| Average | O(n) |
| Worst | O(n) |

Big-O usually refers to **worst case**.

---

### 6. Space (Memory) Complexity

#### What Is Space Complexity?

Space complexity measures:
> How memory usage grows with input size

Includes:
- Variables
- Data structures
- Recursion stack
- Heap allocations

---

#### O(1) — Constant Space

```cpp
int sum = 0;
```

Memory does not grow with input.

---

#### O(n) — Linear Space

```cpp
vector<int> v(n);
```

Memory grows with input size.

---

#### O(n) — Recursion Stack

```cpp
int factorial(int n) {
    if (n == 0) return 1;
    return n * factorial(n - 1);
}
```

Uses O(n) stack space.

---

#### O(n²) — Matrix Allocation

```cpp
vector<vector<int>> matrix(n, vector<int>(n));
```

Memory grows quadratically.

---

### 7. Time vs Space Trade-Off

Often, we trade memory for speed.

Example: Hash table vs linear search

| Approach | Time | Space |
|------|------|------|
| Linear search | O(n) | O(1) |
| Hash table | O(1) avg | O(n) |

---

### 8. Performance Complexity (Real-World)

#### Complexity vs Performance

Big-O does NOT capture:
- Cache behavior
- Branch prediction
- Memory latency
- Parallelism
- Constant factors

Example:

```cpp
vector<int> v;
list<int> l;
```

Both traversal are O(n), but:
- `vector` is much faster (cache-friendly)
- `list` is slower (pointer chasing)

---

### 9. Constant Factors Matter

```cpp
for (int i = 0; i < n; i++) { ... }
```

vs

```cpp
for (int i = 0; i < n; i++) {
    expensive_operation();
}
```

Same Big-O, very different performance.

---

### 10. Measuring Performance in Practice

#### Time Measurement

```cpp
#include <chrono>

auto start = std::chrono::high_resolution_clock::now();
// code
auto end = std::chrono::high_resolution_clock::now();
```

---

#### Memory Measurement

- Heap profiling tools
- Valgrind / Massif
- OS-level metrics

---

### 11. Amortized Complexity

Some operations are expensive occasionally.

Example: `std::vector::push_back`

| Operation | Cost |
|--------|------|
| Most inserts | O(1) |
| Reallocation | O(n) |
| Amortized | O(1) |

---

### 12. Algorithmic Complexity vs System Performance

| Aspect | Algorithm | System |
|-----|----------|-------|
| Focus | Growth rate | Real execution |
| Tools | Big-O | Profilers |
| Examples | sort vs search | cache misses |

Both must be considered.

---

### 13. Common Complexity Mistakes

- Assuming O(1) is always fast
- Ignoring memory access patterns
- Using recursion without stack awareness
- Confusing average and worst case
- Over-optimizing prematurely

---

### 14. Choosing the Right Complexity

General rules:
- Prefer O(n log n) or better
- Avoid O(n²) for large inputs
- Avoid exponential algorithms
- Measure before optimizing

---

### 15. Interview Perspective

What interviewers expect:
- Correct Big-O analysis
- Awareness of space usage
- Understanding of trade-offs
- Practical reasoning

---

### 16. Real-World Example

```cpp
unordered_map<int,int> m;
```

Average lookup: O(1)
Worst case: O(n)
Memory cost: high

Used when speed matters more than memory.

---

### 17. Final Takeaway

- Time complexity measures growth of operations
- Space complexity measures growth of memory
- Performance depends on hardware, cache, and constants
- Big-O guides design, profiling confirms reality

Understanding complexity is essential for writing scalable and efficient software.
