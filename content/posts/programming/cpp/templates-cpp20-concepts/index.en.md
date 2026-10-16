+++
title = "Modern C++ #3: Template Metaprogramming and C++20 Concepts & Constraints"
slug = "templates-cpp20-concepts"
date = '2026-10-16T10:33:00+07:00'
draft = true
tags = ["c++", "templates", "metaprogramming", "sfinae", "concepts", "c++20"]
categories = ["Programming", "C++"]
description = "Exploring the mechanics of generic programming in C++, the intricacies of SFINAE, and the transformative paradigm shift introduced by C++20 Concepts."
summary = "From generic templates, two-phase lookup, and type traits to escaping the SFINAE compiler error diagnostic nightmare via C++20 concepts and requires clauses."
+++

## 1. Generic Programming: The Power of C++ Templates

In conventional object-oriented languages (such as Java or C#), polymorphism is predominantly resolved at runtime (**Runtime Polymorphism**) via dynamic dispatch and virtual method tables (`vtable`). Conversely, C++ commands a powerful mechanism at compile time: **Compile-Time Polymorphism** via the **Template** system.

```cpp
// Generic function supporting any type equipped with operator+
template <typename T>
T add(T a, T b) {
    return a + b;
}
```

When you invoke `add(3, 5)` and `add(2.5, 3.1)`, the C++ compiler instantiates two distinct machine code functions tailored specifically for `int` and `double`. There is zero runtime indirection or virtual call overhead!

---

## 2. How the Template Compilation Pipeline Works

Compilers process templates through **Two-Phase Name Lookup**:
1. **Phase 1 (Template Definition):** Parses basic lexical syntax and inspects **non-dependent names** (symbols independent of template type parameters).
2. **Phase 2 (Template Instantiation):** Triggered when concrete types are provided (e.g., `add<int>`). The compiler substitutes arguments and verifies semantic validity (such as whether required operators exist).

Because the entire template definition must be available at the call site for Phase 2 instantiation, standard C++ template code must reside in **Header files (`.h` / `.hpp`)**, rather than isolated in `.cpp` translation units (prior to the arrival of C++20 Modules).

---

## 3. The SFINAE Era and Type Traits

Templates are immensely powerful, yet prior to C++20 they possessed a notorious drawback: **No native, expressive syntax for type constraints.**

If you inadvertently passed an object lacking `operator+` into `add(a, b)`:
The compiler would vomit dozens of pages of impenetrable diagnostic errors tracing deep into standard library internals.

### What Is SFINAE?
To restrict a template to specific types, C++11/14 developers leveraged an arcane language rule called **SFINAE** (*Substitution Failure Is Not An Error*).

If substituting a concrete type causes an invalid type expression in a function template signature, the compiler quietly discards that candidate overload rather than aborting compilation. Engineers harnessed this behavior alongside `std::enable_if_t`:

```cpp
#include <type_traits>
#include <iostream>

// Enable this overload exclusively if T is an arithmetic type
template <typename T, 
          typename = std::enable_if_t<std::is_arithmetic_v<T>>>
void print_number(T val) {
    std::cout << "Numeric value: " << val << "\n";
}
```

While functional, once constraints compounded, `std::enable_if_t` turned codebases into an unreadable labyrinth of angle brackets and template metaprogramming tricks.

---

## 4. C++20 Concepts: The Long-Awaited Solution

The **C++20** standard dismantled SFINAE complexity by introducing its most anticipated language evolution in 15 years: **Concepts & Constraints**.

A **Concept** is a compile-time predicate that evaluates whether a type satisfies a designated suite of semantic and syntactic requirements.

### 4.1. Defining Concepts with `requires` Clauses

```cpp
#include <concepts>
#include <iostream>

// Define custom concept: T must be an arithmetic type occupying >= 4 bytes
template <typename T>
concept LargeNumber = std::is_arithmetic_v<T> && (sizeof(T) >= 4);

// Approach 1: Constrained auto parameter
void process_large(LargeNumber auto x) {
    std::cout << "Processing large number: " << x << "\n";
}

// Approach 2: Explicit requires clause
template <typename T>
    requires LargeNumber<T>
T calculate(T a, T b) {
    return a * b;
}
```

### 4.2. Method and Expression Constraints via `requires`-Expressions

Concepts can mandate member function signatures, return types, and expression validity:

```cpp
// Require that type T provides a .serialize() member returning std::string
template <typename T>
concept Serializable = requires(T obj) {
    { obj.serialize() } -> std::same_as<std::string>;
};

struct User {
    std::string name;
    std::string serialize() { return "{\"user\": \"" + name + "\"}"; }
};

struct NonSerializableItem {};

void save_to_database(Serializable auto entity) {
    std::cout << "Saving: " << entity.serialize() << "\n";
}
```

If you mistakenly write:
```cpp
NonSerializableItem item;
save_to_database(item); 
```

The C++20 compiler delivers a crisp, pinpoint diagnostic:
> *error: constraints not satisfied for class 'NonSerializableItem'. 'obj.serialize()' is not a valid expression.*

---

## 5. Comprehensive Comparison: SFINAE vs. C++20 Concepts

| Metric | SFINAE (C++11 / C++14) | C++20 Concepts |
| :--- | :--- | :--- |
| **Syntax** | Arcane, relying on `std::enable_if` hacks in signatures | Clean, declarative via `concept` and `requires` keywords |
| **Diagnostics** | Hundreds of lines of deep template stack traces | Clear, pointing directly to the unsatisfied predicate |
| **Compilation Speed** | Slow due to exhaustive overload elimination passes | **Significantly faster**; parsed directly by the compiler frontend |
| **Reusability** | Difficult; often reimplemented per function | Modular first-class entities (`std::integral`, `std::ranges::range`) |

---

## 6. Summary

Concepts are far more than syntactic sugar; they fundamentally reshape how developers architect generic software in modern C++. The Standard Template Library from C++20 onward has been overhauled to exploit Concepts natively.

In [Article 4: The Standard Template Library: Containers, Iterators, and C++20 Ranges](../stl-containers-cpp20-ranges/), we examine how Concepts harmonize with the Ranges library to deliver composable functional pipelines.
