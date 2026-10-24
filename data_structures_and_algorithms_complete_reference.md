= Data Structures and Algorithms — Complete Reference with C++ Examples
:toc:
:toclevels: 4
:sectnums:
:source-highlighter: coderay

== 1. Introduction

Data Structures define **how data is stored**.  
Algorithms define **how data is processed**.

Good software performance depends on:
* Correct data structure choice
* Efficient algorithm selection
* Understanding time and space complexity

---

== 2. Complexity Analysis (Big-O)

| Notation | Meaning |
|--------|--------|
| O(1) | Constant |
| O(log n) | Logarithmic |
| O(n) | Linear |
| O(n log n) | Linearithmic |
| O(n²) | Quadratic |

[source,cpp]
----
for(int i=0;i<n;i++) {}   // O(n)
----

---

= PART I — DATA STRUCTURES

== 3. Arrays

=== Description
* Fixed-size
* Contiguous memory
* Fast access

[source,cpp]
----
int arr[5] = {1,2,3,4,5};
----

Access: O(1)  
Insert/Delete: O(n)

---

== 4. Dynamic Array (Vector)

[source,cpp]
----
#include <vector>
std::vector<int> v = {1,2,3};
v.push_back(4);
----

* Dynamic resizing
* Amortized O(1) insert at end

---

== 5. Linked List

=== Singly Linked List

[source,cpp]
----
struct Node {
    int data;
    Node* next;
};
----

Insert/Delete: O(1)  
Search: O(n)

---

== 6. Stack (LIFO)

[source,cpp]
----
#include <stack>
std::stack<int> st;
st.push(1);
st.pop();
----

Operations: push, pop, top  
All O(1)

---

== 7. Queue (FIFO)

[source,cpp]
----
#include <queue>
std::queue<int> q;
q.push(1);
q.pop();
----

Used in:
* Scheduling
* BFS

---

== 8. Deque

[source,cpp]
----
#include <deque>
std::deque<int> d;
d.push_front(1);
d.push_back(2);
----

Insert/delete both ends.

---

== 9. Hash Table

[source,cpp]
----
#include <unordered_map>
std::unordered_map<int,int> um;
um[1] = 100;
----

Average lookup: O(1)  
Worst: O(n)

---

== 10. Tree

=== Binary Tree Node

[source,cpp]
----
struct Node {
    int data;
    Node* left;
    Node* right;
};
----

---

== 11. Binary Search Tree (BST)

[source,cpp]
----
bool search(Node* root, int key) {
    if(!root) return false;
    if(root->data == key) return true;
    if(key < root->data)
        return search(root->left, key);
    return search(root->right, key);
}
----

Average: O(log n)  
Worst: O(n)

---

== 12. Heap

[source,cpp]
----
#include <queue>
std::priority_queue<int> pq;
pq.push(10);
pq.top();
----

Used in:
* Scheduling
* Dijkstra
* Top-K problems

---

== 13. Graph

=== Representation (Adjacency List)

[source,cpp]
----
#include <vector>
std::vector<std::vector<int>> graph(n);
graph[u].push_back(v);
----

---

== 14. Trie

Used for:
* Prefix search
* Autocomplete

[source,cpp]
----
struct Trie {
    bool end;
    Trie* next[26];
};
----

---

== 15. Disjoint Set (Union-Find)

[source,cpp]
----
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
----

Used in:
* Kruskal algorithm
* Connectivity problems

---

= PART II — ALGORITHMS

== 16. Sorting Algorithms

=== Bubble Sort (O(n²))

[source,cpp]
----
void bubble(int a[], int n){
    for(int i=0;i<n;i++)
        for(int j=0;j<n-i-1;j++)
            if(a[j]>a[j+1])
                std::swap(a[j],a[j+1]);
}
----

---

=== Quick Sort (O(n log n) avg)

[source,cpp]
----
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
----

---

=== Merge Sort

[source,cpp]
----
void mergeSort(vector<int>& v){
    if(v.size()<2) return;
}
----

Stable, O(n log n)

---

== 17. Searching Algorithms

=== Linear Search

[source,cpp]
----
for(int x: v)
    if(x==key) return true;
----

---

=== Binary Search

[source,cpp]
----
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
----

---

== 18. Graph Algorithms

=== BFS

[source,cpp]
----
void bfs(int s){
    queue<int> q;
    q.push(s);
}
----

Used for:
* Shortest path (unweighted)
* Level order traversal

---

=== DFS

[source,cpp]
----
void dfs(int u){
    visited[u]=true;
    for(int v: graph[u])
        if(!visited[v]) dfs(v);
}
----

---

== 19. Shortest Path Algorithms

=== Dijkstra

[source,cpp]
----
priority_queue<pair<int,int>> pq;
----

Used for:
* Weighted graphs (non-negative)

---

=== Bellman-Ford

Handles negative edges.

---

== 20. Minimum Spanning Tree

=== Kruskal

[source,cpp]
----
sort(edges.begin(),edges.end());
----

Uses:
* Disjoint Set

---

=== Prim

Uses:
* Priority Queue

---

== 21. Dynamic Programming (DP)

=== Fibonacci

[source,cpp]
----
int fib(int n){
    if(n<=1) return n;
    return fib(n-1)+fib(n-2);
}
----

Optimized with memoization.

---

== 22. Greedy Algorithms

Example:
* Activity selection
* Coin change (specific cases)

---

== 23. Backtracking

Example:
* N-Queens
* Sudoku

[source,cpp]
----
void solve(){
    if(valid) solve();
}
----

---

== 24. Famous Algorithms (Most Used)

* Binary Search
* Quick Sort
* Merge Sort
* BFS / DFS
* Dijkstra
* Kruskal
* Union-Find
* KMP
* Sliding Window
* Two Pointers

---

== 25. Choosing Data Structure & Algorithm

| Problem | Best Choice |
|------|-------------|
| Fast lookup | Hash table |
| Sorted data | BST / heap |
| Shortest path | Dijkstra |
| Connectivity | DSU |
| Prefix match | Trie |

---

== 26. Common Mistakes

* Using O(n²) unintentionally
* Wrong data structure
* Ignoring memory
* Overengineering

---

== 27. Final Takeaway

Data structures and algorithms form the **foundation of efficient software**.

Mastery requires:
* Understanding trade-offs
* Practicing implementations
* Applying correct patterns

This document is a **complete lifelong reference**.

