# C++ Core and Advanced Concepts

A single, de-duplicated reference merged from:
`cpp_complete_reference_basic_core_advanced.md`, `cpp_advanced_concepts_complete_reference.md`,
`implicit_object_lifecycle_cpp.md` and `cpp_value_categories_complete_explanation.md`.

## Contents

1. [Introduction](#introduction)
2. [Basic C++ Concepts](#basic-c-concepts)
3. [Pointers, References and Memory](#pointers-references-and-memory)
4. [Object-Oriented Programming and Polymorphism](#object-oriented-programming-and-polymorphism)
5. [Object Lifetime and Implicit Behavior](#object-lifetime-and-implicit-behavior)
6. [Value Categories and Move Semantics](#value-categories-and-move-semantics)
7. [Callables: Function Pointers, Functors, Lambdas, std::function](#callables-function-pointers-functors-lambdas-stdfunction)
8. [Templates, Exceptions and Other Language Features](#templates-exceptions-and-other-language-features)
9. [STL, Smart Pointers and Concurrency (Quick View)](#stl-smart-pointers-and-concurrency-quick-view)
10. [Undefined Behavior](#undefined-behavior)
11. [Performance and Low-Latency Notes](#performance-and-low-latency-notes)
12. [Design Patterns and C++ vs Python](#design-patterns-and-c-vs-python)
13. [Common Mistakes](#common-mistakes)
14. [Interview Questions and Explanations](#interview-questions-and-explanations)
15. [Best Practices and Takeaways](#best-practices-and-takeaways)

---

## Introduction

C++ is a high-performance, general-purpose programming language supporting:
- Procedural programming
- Object-Oriented Programming
- Generic programming
- Low-level system access

C++ is widely used in:
- Trading systems
- Databases
- Operating systems
- Game engines
- High-performance servers

Advanced C++ concepts focus on runtime polymorphism, object lifetime, indirection and abstraction, performance and memory layout, and correctness and safety. They are critical for system programming, framework design, low-latency applications, interviews and code reviews.

---

## Basic C++ Concepts

### Program Structure

```cpp
#include <iostream>

int main() {
    std::cout << "Hello C++";
    return 0;
}
```

Execution starts from `main()`.

### Variables and Data Types

```cpp
int a = 10;
double b = 3.14;
char c = 'x';
bool flag = true;
```

C++ is statically typed.

### Constants

```cpp
const int x = 10;
constexpr int y = 20;
```

- `const` → read-only
- `constexpr` → compile-time constant

### Operators

```cpp
int a = 10 + 5;     // arithmetic
int b = 10 / 3;
int c = 10 % 3;

if (a > b && c != 0) {}   // comparison and logical
```

### Control Flow

```cpp
if (x > 0) {
    std::cout << "Positive";
} else {
    std::cout << "Negative";
}

for (int i = 0; i < 5; ++i) {
    std::cout << i;
}

while (x > 0) {
    x--;
}
```

### Functions

```cpp
int add(int a, int b) {
    return a + b;
}
```

---

## Pointers, References and Memory

### Pointers and References

```cpp
int x = 10;
int* p = &x;
int& r = x;
```

Pointers store addresses; references are aliases.

### Stack vs Heap

```cpp
int a = 10;          // stack
int* p = new int(5); // heap
delete p;
```

See [Object Lifetime and Scope](#object-lifetime-and-scope) for how destruction differs between the two.

---

## Object-Oriented Programming and Polymorphism

### Classes and Objects

```cpp
class Car {
public:
    void drive() {
        std::cout << "Driving";
    }
};
```

### Encapsulation

```cpp
class Account {
private:
    int balance;
public:
    int getBalance() const { return balance; }
};
```

### Inheritance

```cpp
class Base {};
class Derived : public Base {};
```

### Polymorphism and Virtual Functions

A virtual function supports **runtime polymorphism**: the call is resolved at **runtime**, not compile time.

```cpp
class Base {
public:
    virtual void show() {
        std::cout << "Base";
    }
};

class Derived : public Base {
public:
    void show() override {}
};
```

Virtual functions allow:
- Interface-based programming
- Behavior override
- Substitution (Liskov principle)

### Virtual Destructor (Critical)

Problem without a virtual destructor:

```cpp
class Base {
public:
    ~Base() {}
};

class Derived : public Base {
    int* p = new int;
public:
    ~Derived() { delete p; }
};

Base* b = new Derived();
delete b;   // ❌ Derived destructor NOT called
```

This causes **resource leaks**. Correct solution:

```cpp
class Base {
public:
    virtual ~Base() {}
};
```

Rule:
> **If a class has ANY virtual function, its destructor should be virtual.**

### vtable (Virtual Table)

A **vtable** is a compiler-generated table of function pointers used for dynamic dispatch.
Each polymorphic object contains a hidden pointer (`vptr`) to its vtable, and each derived class has its own vtable.

```text
object -> vptr -> vtable -> function
```

Memory layout (conceptual):

```text
[ vptr ][ data members ]
```

Call cost:
- One extra indirection
- Usually negligible

### override and final

```cpp
class Derived : public Base {
public:
    void foo() override;
};
```

`override` gives compile-time checking and prevents signature mistakes.

```cpp
class Derived final {};
void foo() final;
```

`final` prevents further inheritance or override.

### Abstract Classes and Interfaces

```cpp
class Shape {
public:
    virtual double area() const = 0;   // pure virtual
};
```

- Cannot instantiate
- Defines an interface

### Multiple and Virtual Inheritance

```cpp
class A {};
class B {};
class C : public A, public B {};
```

Problems:
- Ambiguity
- Diamond problem

Virtual inheritance solves the diamond problem:

```cpp
class A {};
class B : virtual public A {};
class C : virtual public A {};
class D : public B, public C {};
```

### Object Slicing

```cpp
Derived d;
Base b = d;   // slicing
```

The derived part is lost. Avoid by passing by reference or pointer.

### dynamic_cast and RTTI

```cpp
Base* b = new Derived();
Derived* d = dynamic_cast<Derived*>(b);
typeid(*b).name();
```

RTTI (Run-Time Type Information) enables `dynamic_cast` and `typeid`.

- Requires a polymorphic base and a runtime check
- Cost: RTTI lookup — avoid RTTI in hot paths

### Empty Base Optimization (EBO)

```cpp
struct Empty {};
struct Holder : Empty {
    int x;
};
```

An empty base occupies no space. Used heavily in the STL.

---

## Object Lifetime and Implicit Behavior

In C++, many operations related to object creation, copying, moving, and destruction happen **implicitly**: the compiler generates code on your behalf, certain functions exist even if you never write them, and they are automatically called at specific points in an object’s lifetime. This behavior is governed by strict language rules and is deterministic.

Understanding it is critical for resource management, exception safety, correct object design, and system-level C++ interviews.

### Special Member Functions

C++ defines **six special member functions**. If not explicitly declared, the compiler may generate them automatically:

1. Default constructor
2. Destructor
3. Copy constructor
4. Copy assignment operator
5. Move constructor
6. Move assignment operator

```cpp
class A {
public:
    A() { }
    ~A() { }
};
```

### Example Class Used Throughout

```cpp
class File {
    std::ofstream f;
public:
    File() : f("data.txt") {}
};
```

This class opens a file during construction but does not explicitly close it.

### Implicit Destructor and Member Destruction

Although no destructor is written, the compiler generates one:

```cpp
File::~File() {
    // empty body
}
```

C++ guarantees:

> All non-static data members are destroyed automatically when an object is destroyed.

Destruction order:
1. Destructor body executes
2. Members are destroyed in **reverse order of declaration**

Even an empty destructor still triggers destruction of members.

### RAII: Where the File Is Actually Closed

When `File` is destroyed:
1. `File::~File()` runs
2. `std::ofstream::~ofstream()` runs
3. The file descriptor is closed inside `std::ofstream`’s destructor

The programmer never explicitly calls `close()`. This pattern is **RAII (Resource Acquisition Is Initialization)** and ensures deterministic cleanup:

```cpp
#include <fstream>

void write() {
    std::ofstream f("data.txt");
    f << "Hello";
} // file closed automatically
```

### Object Lifetime and Scope

Automatic storage duration (stack objects):

```cpp
void foo() {
    File f;
} // f goes out of scope here
```

At scope exit the destructor is called automatically, the file is closed, and no leaks are possible.

Dynamic storage duration (heap objects):

```cpp
File* f = new File();
// ...
delete f;
```

`delete` explicitly triggers destruction; forgetting `delete` prevents destructor execution.

### Exception Safety and Implicit Destruction

```cpp
void foo() {
    File f;
    throw std::runtime_error("error");
}
```

Despite the exception, stack unwinding occurs, `File::~File()` is still called and the file is safely closed. This is a fundamental guarantee of C++.

### Implicit Copy Operations (Deleted Here)

If allowed, the compiler would generate:

```cpp
File::File(const File& other) : f(other.f) {}
```

But `std::ofstream` is NOT copyable, so the compiler **deletes** the copy constructor and copy assignment:

```cpp
File a;
File b = a; // compile-time error
b = a;      // compile-time error
```

This prevents double-close and undefined behavior.

### Implicit Move Operations

Since `std::ofstream` is movable, the compiler generates:

```cpp
File::File(File&& other) : f(std::move(other.f)) {}
File& operator=(File&& other);
```

Ownership of the file handle is transferred; the moved-from object no longer owns the file.

### Implicit Default Constructor

If **no** constructor is defined, the compiler generates a default constructor.
Because `File() : f("data.txt") {}` is explicitly defined, no implicit default constructor is generated.

### Summary Table of Implicit Behavior

| Feature | Implicitly Generated |
|------|----------------------|
| Destructor | Yes |
| Member destruction | Yes |
| File close | Yes |
| Copy constructor | Deleted |
| Copy assignment | Deleted |
| Move constructor | Yes |
| Move assignment | Yes |

### Rule of 0 / 3 / 5

- **Rule of 0** (preferred modern C++ style): if a class uses RAII types, do not write a destructor or copy/move logic
- **Rule of 3**: if a class manages a resource manually, define destructor, copy constructor, copy assignment
- **Rule of 5**: additionally define move constructor and move assignment

```cpp
class Safe {
    std::unique_ptr<int> p;
};
```

Avoid manual memory management.

### Why Implicit Behavior Is Powerful

- Deterministic cleanup
- Exception safety
- Zero-cost abstractions
- Cleaner code
- Fewer bugs

Manual cleanup is error-prone and discouraged.

---

## Value Categories and Move Semantics

### Why Value Categories Exist

C++ must answer this question for every expression:

> Does this expression refer to an object with a stable identity, or just a temporary value?

The answer determines:
- Can it be assigned to?
- Can its address be taken?
- Can it be moved from?
- Can it bind to references?
- How efficiently can code be generated?

### The Big Picture (Hierarchy)

```text
                expression
                   |
          ---------------------
          |                   |
       glvalue               rvalue
          |                   |
     ------------         ------------
     |          |         |          |
   lvalue     xvalue    xvalue     prvalue
```

- lvalue + xvalue = glvalue
- xvalue belongs to BOTH glvalue and rvalue
- prvalue is a pure temporary

This overlap is intentional and enables move semantics.

### lvalue — "Has Identity"

An lvalue refers to a named object, has a stable memory address, and exists beyond the current expression.
Think: “This object lives somewhere.”

```cpp
int x = 10;
x;          // lvalue

int arr[3];
arr[0];     // lvalue

std::string s = "hello";
s;          // lvalue

x = 20;     // OK
&x;         // OK
```

Rule of thumb:
> If you can safely take its address, it is an lvalue.

### rvalue — "Temporary Value"

An rvalue has no stable identity, represents a temporary result, and typically dies at the end of the full expression.
Think: “This is just a value, not a place.”

```cpp
10;         // rvalue
x + 5;      // rvalue
foo();      // usually rvalue

(x + 5) = 10;   // ERROR
&(x + 5);       // ERROR
```

You cannot assign to or take the address of an rvalue.

### prvalue — "Pure rvalue"

A pure temporary value with no identity that exists only to compute a result.
Think: “Just a value flowing through expressions.”

```cpp
42;                     // prvalue
x + y;                  // prvalue
std::string("abc");     // prvalue
return 5;               // prvalue
```

- No address
- No identity
- Cannot be directly moved from (until materialized)

### xvalue — "Expiring Value" (Most Important)

An xvalue refers to a real object with identity that is about to expire, signalling that it is safe to move from.
Think: “This object exists, but I’m done with it.”

#### How xvalues Are Created

`std::move`:

```cpp
std::string s = "hello";
std::string t = std::move(s);
```

- `s` → lvalue
- `std::move(s)` → xvalue

> [!IMPORTANT]
> `std::move` does NOT move anything.
> It only casts to an xvalue.

Return by value (conceptually):

```cpp
std::string make() {
    std::string s = "hi";
    return s;   // s treated as an xvalue → move instead of copy
}
```

Member access on temporaries:

```cpp
std::string("abc").substr(1);
```

- `std::string("abc")` → prvalue
- Member access produces an xvalue

#### Why xvalues Are Necessary

Without xvalues, move semantics would not exist, everything would be copied, and performance would suffer badly.
They enable efficient transfers of resources, safe reuse of temporaries, and zero-cost abstractions.

### Move Semantics

```cpp
std::vector<int> v2 = std::move(v1);
```

Moves resources instead of copying.

### Reference Binding Rules (Critical)

```cpp
void f(int& x);        // lvalue reference
void g(const int& x);  // const lvalue reference
void h(int&& x);       // rvalue reference
```

| Reference Type | Can Bind To |
|--------------|------------|
| T&           | lvalue only |
| const T&     | lvalue + rvalue |
| T&&          | rvalue + xvalue |

```cpp
int x = 10;
f(x);            // OK
f(10);           // ERROR
g(x);            // OK
g(10);           // OK
h(10);           // OK
h(std::move(x)); // OK
```

### Perfect Forwarding

```cpp
template<typename T>
void wrapper(T&& arg) {
    process(std::forward<T>(arg));
}
```

- If caller passes lvalue → forwarded as lvalue
- If caller passes rvalue → forwarded as rvalue

### auto&& (Universal Reference)

```cpp
auto&& x = expr;
```

- If expr is lvalue → x becomes T&
- If expr is rvalue → x becomes T&&

### Common Bugs Due to Misunderstanding

Using moved-from objects:

```cpp
std::string s = "hello";
auto t = std::move(s);
std::cout << s; // VALID but unspecified state
```

Accidental copies instead of moves:

```cpp
void f(std::string s);

std::string x = "hi";
f(x);            // copy
f(std::move(x)); // move
```

### Mental Model

- lvalue → “I can name it”
- prvalue → “Just a value”
- xvalue → “I’m done with it, take it”
- rvalue → “Temporary in general”

---

## Callables: Function Pointers, Functors, Lambdas, std::function

### Function Pointers

A variable that stores the address of a function.

```cpp
int add(int a, int b) { return a + b; }

int (*fp)(int, int) = add;
fp(2, 3);
```

Used in C APIs, callbacks, and legacy systems.

### Functors (Function Objects)

An object that behaves like a function.

```cpp
struct Add {
    int operator()(int a, int b) const {
        return a + b;
    }
};

Add f;
f(2, 3);
```

Advantages over function pointers:
- Can hold state
- Inlineable
- Type-safe

### Lambdas (Modern Functors)

```cpp
auto add = [](int a, int b) {
    return a + b;
};
```

Lambdas are compiler-generated functors — lightweight and preferred in modern C++.

### std::function

A **type-erased callable wrapper**.

```cpp
std::function<int(int,int)> f;
f = add;
f = Add{};
f = [](int a,int b){ return a+b; };
```

Pros: flexible, uniform interface.
Cons: runtime overhead, possible heap allocation.

### Comparison

| Feature | Function Pointer | Functor | std::function |
|------|-----------------|---------|---------------|
| State | No | Yes | Yes |
| Inline | Yes | Yes | No (usually) |
| Overhead | None | None | Medium |
| Flexibility | Low | Medium | High |

### Type Erasure

Type erasure hides concrete types behind a uniform interface.

```cpp
std::function<void()> f;
```

Used in `std::function`, `std::any`, and `std::shared_ptr` (custom deleter).

---

## Templates, Exceptions and Other Language Features

### Templates

```cpp
template<typename T>
T add(T a, T b) {
    return a + b;
}
```

### Exception Handling

```cpp
try {
    throw std::runtime_error("error");
} catch (const std::exception& e) {
    std::cout << e.what();
}
```

### noexcept

```cpp
void f() noexcept;
```

Used for move semantics, optimization, and program correctness.

### Const-Correctness

```cpp
void foo() const;
```

Improves safety, enables optimizations, and improves API clarity.

### Inline Functions

```cpp
inline int add(int a, int b) {
    return a + b;
}
```

Hints the compiler to inline; modern compilers decide automatically.

---

## STL, Smart Pointers and Concurrency (Quick View)

### STL

```cpp
std::vector<int> v = {1,2,3};
std::map<int,int> m;

std::sort(v.begin(), v.end());

for (auto it = v.begin(); it != v.end(); ++it) {}
```

### Smart Pointers

```cpp
#include <memory>

std::unique_ptr<int> u = std::make_unique<int>(10);
std::shared_ptr<int> s = std::make_shared<int>(20);
```

### Multithreading, Mutex and Atomics

```cpp
#include <thread>
#include <mutex>
#include <atomic>

void task() {}

std::thread t(task);
t.join();

std::mutex m;
std::lock_guard<std::mutex> lock(m);

std::atomic<int> counter{0};
counter++;
```

Synchronization ensures happens-before relationships and visibility across threads.

---

## Undefined Behavior

Undefined behavior = program is invalid. Examples:
- Accessing out-of-bounds
- Data races
- Using a deleted object
- Violating strict aliasing

### Strict Aliasing Rule

```cpp
float f = 1.0f;
int* p = (int*)&f;   // UB
```

Use `memcpy` instead.

---

## Performance and Low-Latency Notes

### Low-Latency Programming Concepts

- Avoid heap allocations in hot paths
- Avoid locks where possible
- Prefer cache-friendly data
- Use fixed-size containers

### Memory Alignment

```cpp
alignas(64) int x;
```

Used for cache-line alignment and performance tuning.

### Low-Level Performance Notes

- Virtual calls are cheap but unpredictable
- Branch misprediction costs more
- Cache misses dominate cost
- Prefer composition over inheritance
- Measure, don’t guess

---

## Design Patterns and C++ vs Python

### Singleton

```cpp
class Singleton {
public:
    static Singleton& instance() {
        static Singleton s;
        return s;
    }
};
```

### Factory (Concept)

```text
Create objects without exposing creation logic
```

### C++ vs Python (Quick View)

| C++ | Python |
|----|-------|
| Compiled | Interpreted |
| Fast | Slower |
| Manual memory control | GC |
| Low-level | High-level |

---

## Common Mistakes

- Missing virtual destructor
- Assuming destructors are optional
- Writing unnecessary destructors
- Forgetting delete on heap objects
- Copying non-copyable resources
- Ignoring move semantics
- Overusing inheritance
- Ignoring object slicing
- Using `std::function` in hot paths
- Using RTTI unnecessarily

---

## Interview Questions and Explanations

### Common Interview Questions

- Difference between `const` and `constexpr`?
- Why virtual destructor?
- vector vs list?
- map vs unordered_map?
- What is RAII?
- What happens if std::thread not joined?

### Implicit Object Behavior

> In C++, even if we don’t write destructors or copy logic, the compiler generates them based on strict rules.
> When an object goes out of scope, its destructor runs, and all members are destroyed automatically.
> Since `std::ofstream` closes the file in its destructor, the file is closed implicitly.
> This behavior is deterministic, exception-safe, and is the foundation of RAII.

### Value Categories

> An lvalue has identity and a stable address.
> A prvalue is a pure temporary with no identity.
> An xvalue represents an object that still exists but is about to expire and can be safely moved from.
> These categories enable move semantics and perfect forwarding.

### Advanced C++ Summary

Advanced C++ requires deep understanding of:
- Object lifetime
- Polymorphism internals
- Abstraction costs
- Language rules
- Undefined behavior

Correctness comes before optimization.

---

## Best Practices and Takeaways

### Best Practices

- Prefer modern C++
- Use RAII everywhere
- Avoid raw pointers
- Prefer STL algorithms
- Measure before optimizing
- Write readable code

### One-Line Takeaways

- C++ provides fine-grained control over performance and resources, making it ideal for building safe, efficient, and scalable systems when used with modern best practices.
- Implicit object behavior means the compiler automatically manages construction, destruction, and resource cleanup according to strict lifetime rules — even when the programmer writes no explicit code.
- Value categories describe whether an expression has identity, lifetime, and move eligibility — they are the foundation of modern C++ performance and correctness.
- Advanced C++ is about **control with responsibility**; understanding these concepts separates **users of C++** from **C++ engineers**.
