# Core Java — Interview Notes (Freshers) Part 2

## Table of Contents

| # | Question |
|---|---|
| **Main Method** | |
| 1 | [Why is the main method static?](#1-why-is-the-main-method-static) |
| 2 | [Can we run main() without the static keyword?](#2-can-we-run-main-without-the-static-keyword) |
| 3 | [Does the order of public and static matter in main?](#3-does-the-order-of-public-and-static-matter-in-main) |
| 4 | [Can main be declared as final or private?](#4-can-main-be-declared-as-final-or-private) |
| **Class Execution** | |
| 5 | [Can we run a Java class without a main method?](#5-can-we-run-a-java-class-without-a-main-method) |
| 6 | [Can a class be declared as final?](#6-can-a-class-be-declared-as-final) |
| **Static vs Non-Static** | |
| 7 | [Can a static method reference a non-static variable?](#7-can-a-static-method-reference-a-non-static-variable) |
| 8 | [Can we override static variables?](#8-can-we-override-static-variables) |
| **Keywords and Identifiers** | |
| 9 | [Is main a keyword in Java?](#9-is-main-a-keyword-in-java) |

---

## 1. Why is the main method static?

The JVM needs to call `main()` **without creating an instance** of the class. Static methods belong to the class itself, so the JVM can invoke it directly.

```java
public class App {
    public static void main(String[] args) {
        // JVM calls this directly — no object of App is created
        System.out.println("Started");
    }
}
```

If `main` were non-static, the JVM would need to create an object first — but it wouldn't know which constructor to use or what arguments to pass.

---

## 2. Can we run main() without the static keyword?

The JVM specifically looks for `public static void main(String[] args)`. Without `static`, the program compiles but throws a runtime error.

```java
public class App {
    public void main(String[] args) { // missing static
        System.out.println("Hello");
    }
}
// Compiles fine but JVM throws:
// Error: Main method not found in class App
```

---

## 3. Does the order of public and static matter in main?

The order of access modifiers and `static` does not matter. Both are valid:

```java
public static void main(String[] args) { } // standard
static public void main(String[] args) { } // also valid
```

The JVM looks for the combination of `public`, `static`, `void`, and the method name `main` — not their order.

---

## 4. Can main be declared as final or private?

| Modifier | Allowed | Reason |
|---|---|---|
| `final` | Yes | Prevents overriding — but `main` is rarely overridden anyway |
| `private` | No | JVM requires `main` to be `public` to access it from outside |
| `protected` | No | JVM requires `public` |

```java
public static final void main(String[] args) { } // valid — compiles and runs
// private static void main(String[] args) { }   // JVM cannot access — runtime error
```

---

## 5. Can we run a Java class without a main method?

| Scenario | Possible |
|---|---|
| Standalone application | No — JVM needs `main` as entry point |
| Class called by another class | Yes — any public method can be invoked |
| Web application (Servlet, Spring) | Yes — framework manages the lifecycle |
| JUnit test class | Yes — test runner invokes test methods directly |

```java
// No main — but can be called from another class
public class MathUtils {
    public int add(int a, int b) { return a + b; }
}

// Caller class has main
public class App {
    public static void main(String[] args) {
        MathUtils m = new MathUtils();
        System.out.println(m.add(2, 3)); // 5
    }
}
```

---

## 6. Can a class be declared as final?

**Yes.** A `final` class **cannot be extended** (subclassed).

```java
public final class Constants {
    public static final double PI = 3.14159;
}

// class MyConstants extends Constants { } // compile error
```

Well-known `final` classes in Java:

| Class | Why final |
|---|---|
| `String` | Immutability guarantee |
| `Integer`, `Double`, etc. | Wrapper class integrity |
| `Math` | Utility class, no need to extend |

**Gotcha**: A `final` class can still implement interfaces — it just cannot be subclassed.

---

## 7. Can a static method reference a non-static variable?

Static methods belong to the class — they exist before any object is created. Non-static (instance) variables belong to a specific object.

```java
public class Counter {
    int count = 0; // instance variable

    public static void reset() {
        count = 0; // compile error — cannot access instance variable from static context
    }

    public static void printDefault() {
        Counter c = new Counter(); // must create an object first
        c.count = 0;               // then access via object reference
    }
}
```

**Rule**: Static context → can only access static members directly. To access instance members, create an object first.

---

## 8. Can we override static variables?

Static variables cannot be overridden — they can only be **hidden**.

```java
class Parent {
    static String name = "Parent";
}

class Child extends Parent {
    static String name = "Child"; // variable hiding, NOT overriding
}

Parent p = new Child();
System.out.println(p.name); // "Parent" — resolved by reference type, not object type
```

| | Overriding | Variable Hiding |
|---|---|---|
| Applies to | Instance methods | Static variables / static methods |
| Resolved by | Object type (runtime) | Reference type (compile time) |
| Polymorphism | Yes | No |

**Gotcha**: This is why you should always access static members via the class name (`Parent.name`), not via a reference variable.

---

## 9. Is main a keyword in Java?

`main` is an **identifier** — a method name chosen by convention. It has no special meaning to the compiler.

| Term | Examples |
|---|---|
| **Keywords** — reserved, cannot be used as identifiers | `class`, `static`, `void`, `if`, `else`, `for`, `while`, `int`, `boolean`, `final`, `abstract`, `extends`, `implements`, `try`, `catch`, `return`, `new`, `this`, `super` |
| **Identifiers** — names given by the programmer | `main`, `System`, `println`, `args`, `counter`, `MyClass` |

```java
// main is just a method name — you could name it differently
// but JVM only looks for "main" as the entry point
public static void main(String[] args) { }

// This is valid Java but JVM won't find it as entry point
public static void start(String[] args) { }
```

**Gotcha**: `true`, `false`, and `null` are **literals** — not keywords, but also cannot be used as identifiers.
