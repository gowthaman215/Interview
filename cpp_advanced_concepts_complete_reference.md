# Advanced C++ Concepts — Complete Practical Reference

## 1. Introduction

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

## 2. Virtual Functions

### What is a Virtual Function?

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

### Why Virtual Functions Exist

They allow:
- Interface-based programming
- Behavior override
- Substitution (Liskov principle)

---

## 3. Virtual Destructor (CRITICAL)

### Problem Without Virtual Destructor

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

### Correct Solution

```cpp
class Base {
public:
    virtual ~Base() {}
};
```

Rule:
> **If a class has ANY virtual function, its destructor should be virtual.**

---

## 4. vtable (Virtual Table)

### What is a vtable?

A **vtable** is a compiler-generated table of function pointers used for dynamic dispatch.

Each polymorphic object contains:
- A hidden pointer (`vptr`) to its vtable

---

### How Virtual Call Works

```text
object -> vptr -> vtable -> function
```

Call cost:
- One extra indirection
- Usually negligible

---

### Memory Layout (Conceptual)

```text
[ vptr ][ data members ]
```

Each derived class has its own vtable.

---

## 5. Override and Final

### override

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

### final

```cpp
class Derived final {};
```

or

```cpp
void foo() final;
```

Prevents further inheritance or override.

---

## 6. Abstract Classes and Interfaces

### Pure Virtual Function

```cpp
class Shape {
public:
    virtual double area() const = 0;
};
```

- Cannot instantiate
- Defines an interface

---

## 7. Function Pointers

### What is a Function Pointer?

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

## 8. Functors (Function Objects)

### What is a Functor?

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

## 9. Lambdas (Modern Functors)

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

## 10. std::function

### What is std::function?

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

## 11. Function Pointer vs Functor vs std::function

| Feature | Function Pointer | Functor | std::function |
|------|-----------------|---------|---------------|
| State | No | Yes | Yes |
| Inline | Yes | Yes | No (usually) |
| Overhead | None | None | Medium |
| Flexibility | Low | Medium | High |

---

## 12. Type Erasure (Advanced)

Type erasure hides concrete types behind a uniform interface.

```cpp
std::function<void()> f;
```

Used in:
- std::function
- std::any
- std::shared_ptr (custom deleter)

---

## 13. Dynamic Casting

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

## 14. RTTI (Run-Time Type Information)

RTTI enables:
- dynamic_cast
- typeid

```cpp
typeid(*b).name();
```

Avoid RTTI in hot paths.

---

## 15. Multiple Inheritance

```cpp
class A {};
class B {};
class C : public A, public B {};
```

Problems:
- Ambiguity
- Diamond problem

---

### Virtual Inheritance

```cpp
class A {};
class B : virtual public A {};
class C : virtual public A {};
class D : public B, public C {};
```

Used to solve diamond problem.

---

## 16. Object Slicing

```cpp
Derived d;
Base b = d;   // slicing
```

Derived part is lost.

Avoid by:
- Passing by reference or pointer

---

## 17. Inline Functions

```cpp
inline int add(int a, int b) {
    return a + b;
}
```

Hints compiler to inline.
Modern compilers decide automatically.

---

## 18. Undefined Behavior (Critical)

Examples:
- Accessing out-of-bounds
- Data races
- Using deleted object
- Violating strict aliasing

Undefined behavior = program is invalid.

---

## 19. Strict Aliasing Rule

```cpp
float f = 1.0f;
int* p = (int*)&f;   // UB
```

Use `memcpy` instead.

---

## 20. Const-Correctness

```cpp
void foo() const;
```

Const correctness:
- Improves safety
- Enables optimizations
- Improves API clarity

---

## 21. noexcept

```cpp
void f() noexcept;
```

Used for:
- Move semantics
- Optimization
- Program correctness

---

## 22. Rule of Zero / Three / Five

Prefer:
- Rule of Zero
- RAII-based design

Avoid manual memory management.

---

## 23. Empty Base Optimization (EBO)

```cpp
struct Empty {};
struct Holder : Empty {
    int x;
};
```

Empty base occupies no space.

Used heavily in STL.

---

## 24. Memory Alignment

```cpp
alignas(64) int x;
```

Used for:
- Cache-line alignment
- Performance tuning

---

## 25. Low-Level Performance Notes

- Virtual calls are cheap but unpredictable
- Branch misprediction costs more
- Cache misses dominate cost
- Prefer composition over inheritance
- Measure, don’t guess

---

## 26. Common Advanced Mistakes

- Missing virtual destructor
- Overusing inheritance
- Using std::function in hot paths
- Ignoring object slicing
- Using RTTI unnecessarily

---

## 27. Interview-Grade Summary

Advanced C++ requires deep understanding of:
- Object lifetime
- Polymorphism internals
- Abstraction costs
- Language rules
- Undefined behavior

Correctness comes before optimization.

---

## 28. Final Takeaway

Advanced C++ is about **control with responsibility**.
Understanding these concepts separates **users of C++** from **C++ engineers**.
