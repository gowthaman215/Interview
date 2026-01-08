= C++ Value Categories — Complete Explanation (lvalue, rvalue, prvalue, xvalue)
:toc:
:toclevels: 4
:sectnums:
:source-highlighter: coderay

== 1. Why Value Categories Exist

C++ must answer this question for every expression:

> Does this expression refer to an object with a stable identity, or just a temporary value?

The answer determines:
* Can it be assigned to?
* Can its address be taken?
* Can it be moved from?
* Can it bind to references?
* How efficiently can code be generated?

Value categories are the formal answer to these questions.

---

== 2. The Big Picture (Hierarchy)

C++ expressions are classified as follows:

[source,text]
----
                expression
                   |
          ---------------------
          |                   |
       glvalue               rvalue
          |                   |
     ------------         ------------
     |          |         |          |
   lvalue     xvalue    xvalue     prvalue
----

Important facts:
* lvalue + xvalue = glvalue
* xvalue belongs to BOTH glvalue and rvalue
* prvalue is a pure temporary

This overlap is intentional and enables move semantics.

---

== 3. lvalue — "Has Identity"

=== Definition

An lvalue is an expression that:
* Refers to a named object
* Has a stable memory address
* Exists beyond the current expression

Think:
> “This object lives somewhere.”

---

=== Examples

[source,cpp]
----
int x = 10;
x;          // lvalue
----

[source,cpp]
----
int arr[3];
arr[0];     // lvalue
----

[source,cpp]
----
std::string s = "hello";
s;          // lvalue
----

---

=== Properties

[source,cpp]
----
x = 20;     // OK
&x;         // OK
----

Rule of thumb:
> If you can safely take its address, it is an lvalue.

---

== 4. rvalue — "Temporary Value"

=== Definition

An rvalue is an expression that:
* Does not have a stable identity
* Represents a temporary result
* Typically dies at the end of the full expression

Think:
> “This is just a value, not a place.”

---

=== Examples

[source,cpp]
----
10;         // rvalue
----

[source,cpp]
----
x + 5;      // rvalue
----

[source,cpp]
----
foo();      // usually rvalue
----

---

=== Restrictions

[source,cpp]
----
(x + 5) = 10;   // ERROR
&(x + 5);       // ERROR
----

You cannot assign to or take the address of an rvalue.

---

== 5. prvalue — "Pure rvalue"

=== Definition

A prvalue is:
* A pure temporary value
* Has no identity
* Exists only to compute a result

Think:
> “Just a value flowing through expressions.”

---

=== Examples

[source,cpp]
----
42;                     // prvalue
----

[source,cpp]
----
x + y;                  // prvalue
----

[source,cpp]
----
std::string("abc");     // prvalue
----

[source,cpp]
----
return 5;               // prvalue
----

---

=== Key Properties

* No address
* No identity
* Cannot be directly moved from (until materialized)

---

== 6. xvalue — "Expiring Value" (Most Important)

=== Definition

An xvalue:
* Refers to a real object
* Has identity
* That object is about to expire
* Signals that it is safe to move from

Think:
> “This object exists, but I’m done with it.”

---

== 7. How xvalues Are Created

=== std::move

[source,cpp]
----
std::string s = "hello";
std::string t = std::move(s);
----

Here:
* `s` → lvalue
* `std::move(s)` → xvalue

IMPORTANT:
`std::move` does NOT move anything.
It only casts to an xvalue.

---

=== Return by Value (Conceptually)

[source,cpp]
----
std::string make() {
    std::string s = "hi";
    return s;
}
----

Internally:
* `s` is treated as an xvalue
* Enables move instead of copy

---

=== Member Access on Temporaries

[source,cpp]
----
std::string("abc").substr(1);
----

* `std::string("abc")` → prvalue
* Member access produces an xvalue

---

== 8. Why xvalues Are Necessary

Without xvalues:
* Move semantics would not exist
* Everything would be copied
* Performance would suffer badly

xvalues enable:
* Efficient transfers of resources
* Safe reuse of temporaries
* Zero-cost abstractions

---

== 9. Reference Binding Rules (CRITICAL)

[source,cpp]
----
void f(int& x);        // lvalue reference
void g(const int& x); // const lvalue reference
void h(int&& x);      // rvalue reference
----

Binding rules:

| Reference Type | Can Bind To |
|--------------|------------|
| T&           | lvalue only |
| const T&     | lvalue + rvalue |
| T&&          | rvalue + xvalue |

---

=== Examples

[source,cpp]
----
int x = 10;
f(x);        // OK
f(10);       // ERROR
----

[source,cpp]
----
g(x);        // OK
g(10);       // OK
----

[source,cpp]
----
h(10);       // OK
h(std::move(x)); // OK
----

---

== 10. Perfect Forwarding (Why This Matters)

[source,cpp]
----
template<typename T>
void wrapper(T&& arg) {
    process(std::forward<T>(arg));
}
----

Explanation:
* If caller passes lvalue → forwarded as lvalue
* If caller passes rvalue → forwarded as rvalue

This depends entirely on value categories.

---

== 11. auto&& (Universal Reference)

[source,cpp]
----
auto&& x = expr;
----

Behavior:
* If expr is lvalue → x becomes T&
* If expr is rvalue → x becomes T&&

This only works because of value categories.

---

== 12. Common Bugs Due to Misunderstanding

=== Using Moved-From Objects

[source,cpp]
----
std::string s = "hello";
auto t = std::move(s);
std::cout << s; // VALID but unspecified state
----

---

=== Accidental Copies Instead of Moves

[source,cpp]
----
void f(std::string s);

std::string x = "hi";
f(x);  // copy
f(std::move(x)); // move
----

---

== 13. Mental Model (Best Way to Remember)

* lvalue → “I can name it”
* prvalue → “Just a value”
* xvalue → “I’m done with it, take it”
* rvalue → “Temporary in general”

---

== 14. Interview-Perfect Explanation

> An lvalue has identity and a stable address.  
> A prvalue is a pure temporary with no identity.  
> An xvalue represents an object that still exists but is about to expire and can be safely moved from.  
> These categories enable move semantics and perfect forwarding.

---

== 15. One-Line Takeaway

Value categories describe whether an expression has identity, lifetime, and move eligibility — they are the foundation of modern C++ performance and correctness.

