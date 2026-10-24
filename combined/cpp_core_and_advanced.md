# C++ Core and Advanced Concepts

This file combines the following references into one document:

1. [C++ Programming — Complete Reference (Basic, Core, Advanced)](#part-1) — from `cpp_complete_reference_basic_core_advanced.md`
2. [Advanced C++ Concepts — Complete Practical Reference](#part-2) — from `cpp_advanced_concepts_complete_reference.md`
3. [Implicit Object Behavior and Lifetime Management in C++](#part-3) — from `implicit_object_lifecycle_cpp.md`
4. [C++ Value Categories — Complete Explanation (lvalue, rvalue, prvalue, xvalue)](#part-4) — from `cpp_value_categories_complete_explanation.md`

---

<a id="part-1"></a>

*Part 1 — source: `cpp_complete_reference_basic_core_advanced.md`*

## C++ Programming — Complete Reference (Basic, Core, Advanced)

### Introduction

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

---

### BASIC C++ CONCEPTS

### Program Structure

```cpp
#include <iostream>

int main() {
    std::cout << "Hello C++";
    return 0;
}
```

Execution starts from `main()`.

---

### Variables and Data Types

```cpp
int a = 10;
double b = 3.14;
char c = 'x';
bool flag = true;
```

C++ is statically typed.

---

### Constants

```cpp
const int x = 10;
constexpr int y = 20;
```

- `const` → read-only
- `constexpr` → compile-time constant

---

### Operators

#### Arithmetic

```cpp
int a = 10 + 5;
int b = 10 / 3;
int c = 10 % 3;
```

---

#### Comparison and Logical

```cpp
if (a > b && c != 0) {}
```

---

### Control Flow

#### if / else

```cpp
if (x > 0) {
    std::cout << "Positive";
} else {
    std::cout << "Negative";
}
```

---

#### for Loop

```cpp
for (int i = 0; i < 5; ++i) {
    std::cout << i;
}
```

---

#### while Loop

```cpp
while (x > 0) {
    x--;
}
```

---

### Functions

```cpp
int add(int a, int b) {
    return a + b;
}
```

---

### CORE C++ CONCEPTS

### Pointers and References

```cpp
int x = 10;
int* p = &x;
int& r = x;
```

Pointers store addresses; references are aliases.

---

### Memory Management

#### Stack vs Heap

```cpp
int a = 10;          // stack
int* p = new int(5); // heap
delete p;
```

---

### RAII (Resource Acquisition Is Initialization)

```cpp
#include <fstream>

void write() {
    std::ofstream f("data.txt");
    f << "Hello";
} // file closed automatically
```

RAII ensures deterministic cleanup.

---

### Object-Oriented Programming

### Classes and Objects

```cpp
class Car {
public:
    void drive() {
        std::cout << "Driving";
    }
};
```

---

### Constructors and Destructors

```cpp
class A {
public:
    A() { }
    ~A() { }
};
```

---

### Encapsulation

```cpp
class Account {
private:
    int balance;
public:
    int getBalance() const { return balance; }
};
```

---

### Inheritance

```cpp
class Base {};
class Derived : public Base {};
```

---

### Polymorphism

```cpp
class Base {
public:
    virtual void show() {}
};

class Derived : public Base {
public:
    void show() override {}
};
```

---

### Rule of 0 / 3 / 5

- Rule of 0: Use RAII, no custom destructor
- Rule of 3: Destructor, copy ctor, copy assign
- Rule of 5: + move ctor, move assign

---

### Templates

```cpp
template<typename T>
T add(T a, T b) {
    return a + b;
}
```

---

### Exception Handling

```cpp
try {
    throw std::runtime_error("error");
} catch (const std::exception& e) {
    std::cout << e.what();
}
```

---

### STL (Standard Template Library)

### Containers

```cpp
std::vector<int> v = {1,2,3};
std::map<int,int> m;
```

---

### Algorithms

```cpp
std::sort(v.begin(), v.end());
```

---

### Iterators

```cpp
for (auto it = v.begin(); it != v.end(); ++it) {}
```

---

### ADVANCED C++ CONCEPTS

### Move Semantics

```cpp
std::vector<int> v2 = std::move(v1);
```

Moves resources instead of copying.

---

### Smart Pointers

```cpp
#include <memory>

std::unique_ptr<int> u = std::make_unique<int>(10);
std::shared_ptr<int> s = std::make_shared<int>(20);
```

---

### Lambda Expressions

```cpp
auto f = [](int x) { return x * x; };
```

---

### Multithreading

```cpp
#include <thread>

void task() {}

std::thread t(task);
t.join();
```

---

### Mutex and Synchronization

```cpp
#include <mutex>

std::mutex m;
std::lock_guard<std::mutex> lock(m);
```

---

### Atomic Operations

```cpp
#include <atomic>

std::atomic<int> counter{0};
counter++;
```

---

### Memory Model and Visibility

Synchronization ensures:
- Happens-before relationships
- Visibility across threads

---

### Low-Latency Programming Concepts

- Avoid heap allocations in hot paths
- Avoid locks where possible
- Prefer cache-friendly data
- Use fixed-size containers

---

### Design Patterns (Common)

#### Singleton

```cpp
class Singleton {
public:
    static Singleton& instance() {
        static Singleton s;
        return s;
    }
};
```

---

#### Factory (Concept)

```text
Create objects without exposing creation logic
```

---

### C++ vs Python (Quick View)

| C++ | Python |
|----|-------|
| Compiled | Interpreted |
| Fast | Slower |
| Manual memory control | GC |
| Low-level | High-level |

---

### Common Interview Questions

- Difference between `const` and `constexpr`?
- Why virtual destructor?
- vector vs list?
- map vs unordered_map?
- What is RAII?
- What happens if std::thread not joined?

---

### Best Practices

- Prefer modern C++
- Use RAII everywhere
- Avoid raw pointers
- Prefer STL algorithms
- Measure before optimizing
- Write readable code

---

### One-Line Takeaway

C++ provides fine-grained control over performance and resources, making it ideal for building safe, efficient, and scalable systems when used with modern best practices.

---

<a id="part-2"></a>

*Part 2 — source: `cpp_advanced_concepts_complete_reference.md`*

## Advanced C++ Concepts — Complete Practical Reference

### 1. Introduction

Advanced C++ concepts focus on:
- Runtime polymorphism
- Object lifetime
- Indirection and abstraction
- Performance and memory layout
- Correctness and safety

These concepts are critical for:
- System programming
- Framework design
- Low-latency applications
- Interviews and code reviews

---

### 2. Virtual Functions

#### What is a Virtual Function?

A virtual function supports **runtime polymorphism**.

```cpp
class Base {
public:
    virtual void foo() {
        std::cout << "Base";
    }
};
```

Call is resolved at **runtime**, not compile time.

---

#### Why Virtual Functions Exist

They allow:
- Interface-based programming
- Behavior override
- Substitution (Liskov principle)

---

### 3. Virtual Destructor (CRITICAL)

#### Problem Without Virtual Destructor

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

This causes **resource leaks**.

---

#### Correct Solution

```cpp
class Base {
public:
    virtual ~Base() {}
};
```

Rule:
> **If a class has ANY virtual function, its destructor should be virtual.**

---

### 4. vtable (Virtual Table)

#### What is a vtable?

A **vtable** is a compiler-generated table of function pointers used for dynamic dispatch.

Each polymorphic object contains:
- A hidden pointer (`vptr`) to its vtable

---

#### How Virtual Call Works

```text
object -> vptr -> vtable -> function
```

Call cost:
- One extra indirection
- Usually negligible

---

#### Memory Layout (Conceptual)

```text
[ vptr ][ data members ]
```

Each derived class has its own vtable.

---

### 5. Override and Final

#### override

```cpp
class Derived : public Base {
public:
    void foo() override;
};
```

Benefits:
- Compile-time checking
- Prevents signature mistakes

---

#### final

```cpp
class Derived final {};
```

or

```cpp
void foo() final;
```

Prevents further inheritance or override.

---

### 6. Abstract Classes and Interfaces

#### Pure Virtual Function

```cpp
class Shape {
public:
    virtual double area() const = 0;
};
```

- Cannot instantiate
- Defines an interface

---

### 7. Function Pointers

#### What is a Function Pointer?

A variable that stores the address of a function.

```cpp
int add(int a, int b) { return a + b; }

int (*fp)(int, int) = add;
fp(2, 3);
```

Used in:
- C APIs
- Callbacks
- Legacy systems

---

### 8. Functors (Function Objects)

#### What is a Functor?

An object that behaves like a function.

```cpp
struct Add {
    int operator()(int a, int b) const {
        return a + b;
    }
};
```

Usage:
```cpp
Add f;
f(2, 3);
```

Advantages over function pointers:
- Can hold state
- Inlineable
- Type-safe

---

### 9. Lambdas (Modern Functors)

```cpp
auto add = [](int a, int b) {
    return a + b;
};
```

Lambdas are:
- Compiler-generated functors
- Lightweight
- Preferred in modern C++

---

### 10. std::function

#### What is std::function?

A **type-erased callable wrapper**.

```cpp
std::function<int(int,int)> f;
f = add;
f = Add{};
f = [](int a,int b){ return a+b; };
```

Pros:
- Flexible
- Uniform interface

Cons:
- Runtime overhead
- Possible heap allocation

---

### 11. Function Pointer vs Functor vs std::function

| Feature | Function Pointer | Functor | std::function |
|------|-----------------|---------|---------------|
| State | No | Yes | Yes |
| Inline | Yes | Yes | No (usually) |
| Overhead | None | None | Medium |
| Flexibility | Low | Medium | High |

---

### 12. Type Erasure (Advanced)

Type erasure hides concrete types behind a uniform interface.

```cpp
std::function<void()> f;
```

Used in:
- std::function
- std::any
- std::shared_ptr (custom deleter)

---

### 13. Dynamic Casting

```cpp
Base* b = new Derived();
Derived* d = dynamic_cast<Derived*>(b);
```

Requirements:
- Polymorphic base
- Runtime check

Cost:
- RTTI lookup

---

### 14. RTTI (Run-Time Type Information)

RTTI enables:
- dynamic_cast
- typeid

```cpp
typeid(*b).name();
```

Avoid RTTI in hot paths.

---

### 15. Multiple Inheritance

```cpp
class A {};
class B {};
class C : public A, public B {};
```

Problems:
- Ambiguity
- Diamond problem

---

#### Virtual Inheritance

```cpp
class A {};
class B : virtual public A {};
class C : virtual public A {};
class D : public B, public C {};
```

Used to solve diamond problem.

---

### 16. Object Slicing

```cpp
Derived d;
Base b = d;   // slicing
```

Derived part is lost.

Avoid by:
- Passing by reference or pointer

---

### 17. Inline Functions

```cpp
inline int add(int a, int b) {
    return a + b;
}
```

Hints compiler to inline.
Modern compilers decide automatically.

---

### 18. Undefined Behavior (Critical)

Examples:
- Accessing out-of-bounds
- Data races
- Using deleted object
- Violating strict aliasing

Undefined behavior = program is invalid.

---

### 19. Strict Aliasing Rule

```cpp
float f = 1.0f;
int* p = (int*)&f;   // UB
```

Use `memcpy` instead.

---

### 20. Const-Correctness

```cpp
void foo() const;
```

Const correctness:
- Improves safety
- Enables optimizations
- Improves API clarity

---

### 21. noexcept

```cpp
void f() noexcept;
```

Used for:
- Move semantics
- Optimization
- Program correctness

---

### 22. Rule of Zero / Three / Five

Prefer:
- Rule of Zero
- RAII-based design

Avoid manual memory management.

---

### 23. Empty Base Optimization (EBO)

```cpp
struct Empty {};
struct Holder : Empty {
    int x;
};
```

Empty base occupies no space.

Used heavily in STL.

---

### 24. Memory Alignment

```cpp
alignas(64) int x;
```

Used for:
- Cache-line alignment
- Performance tuning

---

### 25. Low-Level Performance Notes

- Virtual calls are cheap but unpredictable
- Branch misprediction costs more
- Cache misses dominate cost
- Prefer composition over inheritance
- Measure, don’t guess

---

### 26. Common Advanced Mistakes

- Missing virtual destructor
- Overusing inheritance
- Using std::function in hot paths
- Ignoring object slicing
- Using RTTI unnecessarily

---

### 27. Interview-Grade Summary

Advanced C++ requires deep understanding of:
- Object lifetime
- Polymorphism internals
- Abstraction costs
- Language rules
- Undefined behavior

Correctness comes before optimization.

---

### 28. Final Takeaway

Advanced C++ is about **control with responsibility**.
Understanding these concepts separates **users of C++** from **C++ engineers**.

---

<a id="part-3"></a>

*Part 3 — source: `implicit_object_lifecycle_cpp.md`*

## Implicit Object Behavior and Lifetime Management in C++

### Introduction

In C++, many operations related to object creation, copying, moving, and destruction happen **implicitly**.
This means the compiler automatically generates and invokes functions even if the programmer does not explicitly write them.

Understanding this implicit behavior is critical for:
- Resource management
- Exception safety
- Correct object design
- Interview success (especially system-level C++ roles)

---

### What Does "Implicit" Mean in C++?

Implicit behavior means:

- The compiler generates code on your behalf
- Certain functions exist even if you never write them
- These functions are automatically called at specific points in an object’s lifetime

This implicit behavior is governed by strict language rules and is deterministic.

---

### Special Member Functions (Implicitly Generated)

C++ defines **six special member functions**.
If not explicitly declared, the compiler may generate them automatically.

1. Default constructor
2. Destructor
3. Copy constructor
4. Copy assignment operator
5. Move constructor
6. Move assignment operator

These functions control the full lifecycle of an object.

---

### Example Class Used Throughout

```cpp
class File {
    std::ofstream f;
public:
    File() : f("data.txt") {}
};
```

This class opens a file during construction but does not explicitly close it.

---

### Implicit Destructor

Although no destructor is written, the compiler generates one:

```cpp
File::~File() {
    // empty body
}
```

Important:
- Even an empty destructor still triggers destruction of members
- Member destruction happens automatically after the destructor body

---

### Member Destruction Rule (Critical Rule)

C++ guarantees:

> All non-static data members are destroyed automatically when an object is destroyed.

This is non-optional and enforced by the language.

Destruction order:
1. Destructor body executes
2. Members are destroyed in **reverse order of declaration**

---

### Where the File Is Actually Closed

In the example:

```cpp
std::ofstream f;
```

When `File` is destroyed:
1. `File::~File()` runs
2. `std::ofstream::~ofstream()` runs
3. The file descriptor is closed inside `std::ofstream`’s destructor

The programmer never explicitly calls `close()`.

This pattern is known as **RAII (Resource Acquisition Is Initialization)**.

---

### Object Lifetime and Scope

#### Automatic Storage Duration (Stack Objects)

```cpp
void foo() {
    File f;
} // f goes out of scope here
```

At scope exit:
- Destructor is called automatically
- File is closed
- No leaks are possible

---

#### Dynamic Storage Duration (Heap Objects)

```cpp
File* f = new File();
// ...
delete f;
```

Important:
- `delete` explicitly triggers destruction
- Forgetting `delete` prevents destructor execution

---

### Exception Safety and Implicit Destruction

```cpp
void foo() {
    File f;
    throw std::runtime_error("error");
}
```

Despite the exception:
- Stack unwinding occurs
- `File::~File()` is still called
- File is safely closed

This is a fundamental guarantee of C++.

---

### Implicit Copy Constructor

If allowed, the compiler would generate:

```cpp
File::File(const File& other) : f(other.f) {}
```

However:
- `std::ofstream` is NOT copyable
- Therefore, the compiler **deletes** the copy constructor

Result:

```cpp
File a;
File b = a; // compile-time error
```

This prevents double-close and undefined behavior.

---

### Implicit Move Constructor

Since `std::ofstream` is movable, the compiler generates:

```cpp
File::File(File&& other) : f(std::move(other.f)) {}
```

Behavior:
- Ownership of file handle is transferred
- Moved-from object no longer owns the file
- Safe and intentional design

---

### Implicit Copy Assignment Operator

Copy assignment follows the same rule as copy construction.

Because `std::ofstream` is non-copyable:
- Copy assignment is implicitly deleted

```cpp
File a, b;
b = a; // compile-time error
```

---

### Implicit Move Assignment Operator

Move assignment is generated:

```cpp
File& operator=(File&& other);
```

This allows safe reassignment of ownership.

---

### Implicit Default Constructor

If **no** constructor is defined, the compiler generates a default constructor.

In this example:
```cpp
File() : f("data.txt") {}
```

Because a constructor is explicitly defined:
- No implicit default constructor is generated

---

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

---

### Rule of 0, Rule of 3, Rule of 5

#### Rule of 0

If a class uses RAII types:
- Do not write destructor
- Do not write copy/move logic

```cpp
class Safe {
    std::unique_ptr<int> p;
};
```

Preferred modern C++ style.

---

#### Rule of 3

If a class manages a resource manually, define:
- Destructor
- Copy constructor
- Copy assignment

---

#### Rule of 5

Add:
- Move constructor
- Move assignment

---

### Why Implicit Behavior Is Powerful

Implicit object behavior enables:
- Deterministic cleanup
- Exception safety
- Zero-cost abstractions
- Cleaner code
- Fewer bugs

Manual cleanup is error-prone and discouraged.

---

### Common Mistakes

- Assuming destructors are optional
- Forgetting delete on heap objects
- Writing unnecessary destructors
- Copying non-copyable resources
- Ignoring move semantics

---

### Interview-Grade Explanation

> In C++, even if we don’t write destructors or copy logic, the compiler generates them based on strict rules.
> When an object goes out of scope, its destructor runs, and all members are destroyed automatically.
> Since `std::ofstream` closes the file in its destructor, the file is closed implicitly.
> This behavior is deterministic, exception-safe, and is the foundation of RAII.

---

### One-Line Takeaway

Implicit object behavior in C++ means the compiler automatically manages object construction, destruction, and resource cleanup according to strict lifetime rules — even when the programmer writes no explicit code.

---

<a id="part-4"></a>

*Part 4 — source: `cpp_value_categories_complete_explanation.md`*

## C++ Value Categories — Complete Explanation (lvalue, rvalue, prvalue, xvalue)

### 1. Why Value Categories Exist

C++ must answer this question for every expression:

> Does this expression refer to an object with a stable identity, or just a temporary value?

The answer determines:
- Can it be assigned to?
- Can its address be taken?
- Can it be moved from?
- Can it bind to references?
- How efficiently can code be generated?

Value categories are the formal answer to these questions.

---

### 2. The Big Picture (Hierarchy)

C++ expressions are classified as follows:

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

Important facts:
- lvalue + xvalue = glvalue
- xvalue belongs to BOTH glvalue and rvalue
- prvalue is a pure temporary

This overlap is intentional and enables move semantics.

---

### 3. lvalue — "Has Identity"

#### Definition

An lvalue is an expression that:
- Refers to a named object
- Has a stable memory address
- Exists beyond the current expression

Think:
> “This object lives somewhere.”

---

#### Examples

```cpp
int x = 10;
x;          // lvalue
```

```cpp
int arr[3];
arr[0];     // lvalue
```

```cpp
std::string s = "hello";
s;          // lvalue
```

---

#### Properties

```cpp
x = 20;     // OK
&x;         // OK
```

Rule of thumb:
> If you can safely take its address, it is an lvalue.

---

### 4. rvalue — "Temporary Value"

#### Definition

An rvalue is an expression that:
- Does not have a stable identity
- Represents a temporary result
- Typically dies at the end of the full expression

Think:
> “This is just a value, not a place.”

---

#### Examples

```cpp
10;         // rvalue
```

```cpp
x + 5;      // rvalue
```

```cpp
foo();      // usually rvalue
```

---

#### Restrictions

```cpp
(x + 5) = 10;   // ERROR
&(x + 5);       // ERROR
```

You cannot assign to or take the address of an rvalue.

---

### 5. prvalue — "Pure rvalue"

#### Definition

A prvalue is:
- A pure temporary value
- Has no identity
- Exists only to compute a result

Think:
> “Just a value flowing through expressions.”

---

#### Examples

```cpp
42;                     // prvalue
```

```cpp
x + y;                  // prvalue
```

```cpp
std::string("abc");     // prvalue
```

```cpp
return 5;               // prvalue
```

---

#### Key Properties

- No address
- No identity
- Cannot be directly moved from (until materialized)

---

### 6. xvalue — "Expiring Value" (Most Important)

#### Definition

An xvalue:
- Refers to a real object
- Has identity
- That object is about to expire
- Signals that it is safe to move from

Think:
> “This object exists, but I’m done with it.”

---

### 7. How xvalues Are Created

#### std::move

```cpp
std::string s = "hello";
std::string t = std::move(s);
```

Here:
- `s` → lvalue
- `std::move(s)` → xvalue

> [!IMPORTANT]
> `std::move` does NOT move anything.
> It only casts to an xvalue.

---

#### Return by Value (Conceptually)

```cpp
std::string make() {
    std::string s = "hi";
    return s;
}
```

Internally:
- `s` is treated as an xvalue
- Enables move instead of copy

---

#### Member Access on Temporaries

```cpp
std::string("abc").substr(1);
```

- `std::string("abc")` → prvalue
- Member access produces an xvalue

---

### 8. Why xvalues Are Necessary

Without xvalues:
- Move semantics would not exist
- Everything would be copied
- Performance would suffer badly

xvalues enable:
- Efficient transfers of resources
- Safe reuse of temporaries
- Zero-cost abstractions

---

### 9. Reference Binding Rules (CRITICAL)

```cpp
void f(int& x);        // lvalue reference
void g(const int& x); // const lvalue reference
void h(int&& x);      // rvalue reference
```

Binding rules:

| Reference Type | Can Bind To |
|--------------|------------|
| T&           | lvalue only |
| const T&     | lvalue + rvalue |
| T&&          | rvalue + xvalue |

---

#### Examples

```cpp
int x = 10;
f(x);        // OK
f(10);       // ERROR
```

```cpp
g(x);        // OK
g(10);       // OK
```

```cpp
h(10);       // OK
h(std::move(x)); // OK
```

---

### 10. Perfect Forwarding (Why This Matters)

```cpp
template<typename T>
void wrapper(T&& arg) {
    process(std::forward<T>(arg));
}
```

Explanation:
- If caller passes lvalue → forwarded as lvalue
- If caller passes rvalue → forwarded as rvalue

This depends entirely on value categories.

---

### 11. auto&& (Universal Reference)

```cpp
auto&& x = expr;
```

Behavior:
- If expr is lvalue → x becomes T&
- If expr is rvalue → x becomes T&&

This only works because of value categories.

---

### 12. Common Bugs Due to Misunderstanding

#### Using Moved-From Objects

```cpp
std::string s = "hello";
auto t = std::move(s);
std::cout << s; // VALID but unspecified state
```

---

#### Accidental Copies Instead of Moves

```cpp
void f(std::string s);

std::string x = "hi";
f(x);  // copy
f(std::move(x)); // move
```

---

### 13. Mental Model (Best Way to Remember)

- lvalue → “I can name it”
- prvalue → “Just a value”
- xvalue → “I’m done with it, take it”
- rvalue → “Temporary in general”

---

### 14. Interview-Perfect Explanation

> An lvalue has identity and a stable address.
> A prvalue is a pure temporary with no identity.
> An xvalue represents an object that still exists but is about to expire and can be safely moved from.
> These categories enable move semantics and perfect forwarding.

---

### 15. One-Line Takeaway

Value categories describe whether an expression has identity, lifetime, and move eligibility — they are the foundation of modern C++ performance and correctness.
