= Design Patterns in C++ — Complete Reference with Examples
:toc:
:toclevels: 4
:sectnums:
:source-highlighter: coderay

== 1. Introduction

Design Patterns are **reusable solutions to recurring design problems**.

They help:
* Reduce coupling
* Improve maintainability
* Improve extensibility
* Communicate design intent

Patterns are NOT code templates — they are **design ideas**.

---

== 2. Categories of Design Patterns

Design patterns are grouped into:

* Creational Patterns
* Structural Patterns
* Behavioral Patterns

---

= PART I — CREATIONAL PATTERNS

== 3. Singleton

=== Intent
Ensure a class has only one instance.

[source,cpp]
----
class Singleton {
private:
    Singleton() {}
public:
    static Singleton& instance() {
        static Singleton s;
        return s;
    }
};
----

Notes:
* Thread-safe since C++11
* Avoid overuse

---

== 4. Factory Method

=== Intent
Create objects without specifying exact class.

[source,cpp]
----
class Shape {
public:
    virtual void draw() = 0;
};

class Circle : public Shape {
public:
    void draw() override {}
};

class ShapeFactory {
public:
    static std::unique_ptr<Shape> create() {
        return std::make_unique<Circle>();
    }
};
----

---

== 5. Abstract Factory

=== Intent
Create families of related objects.

[source,cpp]
----
class Button {};
class WinButton : public Button {};
class MacButton : public Button {};

class GUIFactory {
public:
    virtual std::unique_ptr<Button> createButton() = 0;
};
----

---

== 6. Builder

=== Intent
Construct complex objects step-by-step.

[source,cpp]
----
class Car {
public:
    std::string engine, wheels;
};

class CarBuilder {
    Car car;
public:
    CarBuilder& buildEngine() {
        car.engine = "V8"; return *this;
    }
    Car build() { return car; }
};
----

---

== 7. Prototype

=== Intent
Clone existing objects.

[source,cpp]
----
class Prototype {
public:
    virtual Prototype* clone() const = 0;
};
----

---

== 8. Object Pool

=== Intent
Reuse expensive objects.

[source,cpp]
----
class Pool {
    std::vector<int> objects;
};
----

---

= PART II — STRUCTURAL PATTERNS

== 9. Adapter

=== Intent
Convert one interface into another.

[source,cpp]
----
class OldAPI {
public:
    void oldCall() {}
};

class Adapter {
    OldAPI api;
public:
    void newCall() { api.oldCall(); }
};
----

---

== 10. Bridge

=== Intent
Separate abstraction from implementation.

[source,cpp]
----
class Renderer {
public:
    virtual void render() = 0;
};

class Shape {
protected:
    Renderer* r;
};
----

---

== 11. Composite

=== Intent
Treat individual objects and compositions uniformly.

[source,cpp]
----
class Component {
public:
    virtual void operation() = 0;
};
----

---

== 12. Decorator

=== Intent
Add behavior dynamically.

[source,cpp]
----
class Coffee {
public:
    virtual int cost() = 0;
};

class Milk : public Coffee {
    Coffee* c;
};
----

---

== 13. Facade

=== Intent
Provide simplified interface.

[source,cpp]
----
class Facade {
public:
    void simpleCall() {}
};
----

---

== 14. Flyweight

=== Intent
Share common state.

[source,cpp]
----
class Flyweight {
    int shared;
};
----

---

== 15. Proxy

=== Intent
Control access to an object.

[source,cpp]
----
class Proxy {
    RealObject obj;
};
----

---

= PART III — BEHAVIORAL PATTERNS

== 16. Strategy

=== Intent
Encapsulate interchangeable algorithms.

[source,cpp]
----
class Strategy {
public:
    virtual void execute() = 0;
};
----

---

== 17. Observer

=== Intent
Notify dependents automatically.

[source,cpp]
----
class Observer {
public:
    virtual void update() = 0;
};
----

---

== 18. Command

=== Intent
Encapsulate request as object.

[source,cpp]
----
class Command {
public:
    virtual void execute() = 0;
};
----

---

== 19. State

=== Intent
Change behavior when state changes.

[source,cpp]
----
class State {
public:
    virtual void handle() = 0;
};
----

---

== 20. Template Method

=== Intent
Define algorithm skeleton.

[source,cpp]
----
class Base {
public:
    void run() {
        step1();
        step2();
    }
    virtual void step1() = 0;
    virtual void step2() = 0;
};
----

---

== 21. Iterator

=== Intent
Access elements sequentially.

[source,cpp]
----
auto it = container.begin();
----

---

== 22. Mediator

=== Intent
Reduce object coupling.

[source,cpp]
----
class Mediator {
public:
    virtual void notify() = 0;
};
----

---

== 23. Memento

=== Intent
Capture object state.

[source,cpp]
----
class Memento {
    int state;
};
----

---

== 24. Chain of Responsibility

=== Intent
Pass request along chain.

[source,cpp]
----
class Handler {
protected:
    Handler* next;
};
----

---

== 25. Visitor

=== Intent
Add operations without modifying classes.

[source,cpp]
----
class Visitor {
public:
    virtual void visit() = 0;
};
----

---

== 26. Interpreter

=== Intent
Define grammar.

[source,cpp]
----
class Expression {
public:
    virtual int interpret() = 0;
};
----

---

== 27. Null Object

=== Intent
Avoid null checks.

[source,cpp]
----
class NullLogger {
public:
    void log() {}
};
----

---

== 28. Dependency Injection (Modern Pattern)

=== Intent
Provide dependencies externally.

[source,cpp]
----
class Service {
public:
    Service(Dependency& d) {}
};
----

---

== 29. Anti-Patterns (Important)

* God Object
* Spaghetti Code
* Overuse of Singleton
* Tight coupling

---

== 30. Choosing the Right Pattern

| Problem | Pattern |
|------|--------|
| One instance | Singleton |
| Algorithm swap | Strategy |
| Object creation | Factory |
| Event handling | Observer |
| Add behavior | Decorator |

---

== 31. Final Takeaway

Design patterns:
* Improve code quality
* Are tools, not rules
* Must be used judiciously

Good design is about **clarity and balance**, not patterns everywhere.

