# Core Java — Interview Notes (Freshers)

## Table of Contents

| # | Question |
|---|---|
| 1 | [What are the features of Java?](#1-what-are-the-features-of-java) |
| 2 | [What is platform independence?](#2-what-is-platform-independence) |
| 3 | [What is the difference between compiler, interpreter, and bytecode?](#3-what-is-the-difference-between-compiler-interpreter-and-bytecode) |
| 4 | [What are the different data types in Java?](#4-what-are-the-different-data-types-in-java) |
| 5 | [What is the difference between declaring, defining, and initializing?](#5-what-is-the-difference-between-declaring-defining-and-initializing) |
| 6 | [How many ways are there to take input in Java?](#6-how-many-ways-are-there-to-take-input-in-java) |
| 7 | [What is a package in Java?](#7-what-is-a-package-in-java) |
| 8 | [Can I import the same package twice? Will JVM load it twice?](#8-can-i-import-the-same-package-twice-will-jvm-load-it-twice) |
| 9 | [Why is Java not 100% object-oriented?](#9-why-is-java-not-100-object-oriented) |
| 10 | [What is Unicode and which system does Java use?](#10-what-is-unicode-and-which-system-does-java-use) |
| 11 | [Which non-Unicode characters can start an identifier?](#11-which-non-unicode-characters-can-start-an-identifier) |
| 12 | [What are primitive types and their wrapper classes?](#12-what-are-primitive-types-and-their-wrapper-classes) |
| 13 | [What methods are inside wrapper classes?](#13-what-methods-are-inside-wrapper-classes) |
| 14 | [What is autoboxing and unboxing?](#14-what-is-autoboxing-and-unboxing) |
| 15 | [What is casting — implicit and explicit?](#15-what-is-casting--implicit-and-explicit) |

---

## 1. What are the features of Java?

| Feature | Description |
|---|---|
| Platform Independence | Write once, run anywhere via JVM bytecode |
| Object-Oriented | Based on objects, classes, inheritance, polymorphism |
| Robust | Strong memory management, garbage collection |
| Secure | Sandboxed execution, no explicit pointers |
| Multithreaded | Built-in support for concurrent programming |
| Portable | Bytecode runs on any device with a JVM |
| High Performance | JIT compiler converts bytecode to native code at runtime |
| Dynamic | Classes loaded at runtime, supports reflection |

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

---

## 2. What is platform independence?

Java source code compiles to **bytecode** (`.class` files) — not machine code. The JVM on each OS interprets the same bytecode.

```
MyProgram.java  →  javac  →  MyProgram.class (bytecode)
                                    ↓
                    JVM (Windows / Linux / macOS)
                                    ↓
                              Program runs
```

```java
// Same code runs on any OS with a JVM
System.out.println("Running on: " + System.getProperty("os.name"));
```

- **JVM** is platform-specific — different per OS
- **Bytecode** is platform-neutral — same everywhere
- **JRE** = JVM + standard libraries

---

## 3. What is the difference between compiler, interpreter, and bytecode?

| | Compiler | Interpreter | Bytecode |
|---|---|---|---|
| What it does | Translates entire source to output before execution | Translates and executes line by line at runtime | Intermediate code — neither source nor machine code |
| Java example | `javac` — `.java` → `.class` | JVM — reads `.class` at runtime | `.class` files |
| Speed | Faster execution | Slower execution | Depends on JVM |
| Platform | Output is platform-specific | Platform-independent | Platform-independent |

Java uses a **hybrid approach**:
1. `javac` compiles source → bytecode
2. JVM interprets bytecode at runtime
3. JIT compiler converts hot bytecode paths → native machine code for speed

---

## 4. What are the different data types in Java?

### Primitive Types (8)

| Type | Size | Range | Default |
|---|---|---|---|
| `byte` | 8-bit | -128 to 127 | 0 |
| `short` | 16-bit | -32,768 to 32,767 | 0 |
| `int` | 32-bit | -2³¹ to 2³¹-1 | 0 |
| `long` | 64-bit | -2⁶³ to 2⁶³-1 | 0L |
| `float` | 32-bit | ~±3.4e38 | 0.0f |
| `double` | 64-bit | ~±1.8e308 | 0.0d |
| `char` | 16-bit | 0 to 65,535 | '\u0000' |
| `boolean` | 1-bit | true / false | false |

### Reference Types

- **Classes** — `String`, `Scanner`, user-defined
- **Interfaces** — abstract types
- **Arrays** — `int[]`, `String[]`
- **Enums** — named constants

```java
int i = 100000;
long l = 10000000000L;  // L suffix required
float f = 3.14f;        // f suffix required
double d = 3.14159265;
char c = 'A';
boolean b = true;
String s = "Hello";     // reference type
```

---

## 5. What is the difference between declaring, defining, and initializing?

| Term | Meaning | Example |
|---|---|---|
| Declaration | Introduces variable name and type | `int counter;` |
| Definition | In Java, same as declaration | `int counter;` |
| Initialization | Assigns a value | `counter = 0;` or `int counter = 0;` |

```java
int counter;        // declaration (+ definition in Java)
counter = 10;       // initialization after declaration

double price = 19.99; // declaration + initialization in one line
```

- **Class/instance variables** — auto-initialized to default values
- **Local variables** — must be initialized before use or compiler error
- **Final variables** — must be initialized at declaration or in constructor

---

## 6. How many ways are there to take input in Java?

| Method | Best For |
|---|---|
| `Scanner` | General purpose, most common |
| `BufferedReader` | Large input, faster I/O |
| `Console` | Secure password input |
| Command-line args | Input at program startup |
| `JOptionPane` | GUI dialog input |

```java
// Scanner — most common
Scanner sc = new Scanner(System.in);
String name = sc.nextLine();
int age = sc.nextInt();
sc.close();

// BufferedReader — faster for large input
BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
String line = br.readLine();
int num = Integer.parseInt(br.readLine());

// Command-line args
public static void main(String[] args) {
    System.out.println(args[0]); // first argument passed at runtime
}
```

---

## 7. What is a package in Java?

A **package** is a namespace that groups related classes, interfaces, and enums.

```java
// Declaration — must be first line in file
package com.example.myapp.model;

public class User { }
```

```java
// Importing
import com.example.myapp.model.User;  // specific class
import java.util.*;                    // all classes in package (wildcard)
```

- Avoids naming conflicts between classes
- Controls access (`package-private` visibility)
- Follows reverse domain naming: `com.company.app`
- Package structure must match directory structure

---

## 8. Can I import the same package twice? Will JVM load it twice?

**Import duplicates** — allowed, compiler silently ignores them. No error.

```java
import java.util.ArrayList;
import java.util.ArrayList; // duplicate — compiler ignores, no error
import java.util.*;
import java.util.*;          // duplicate — compiler ignores, no error
```

**JVM class loading** — each class is loaded **only once**, regardless of how many import statements exist.

- Import statements are compile-time only — they tell the compiler where to find classes
- At runtime, the `ClassLoader` caches loaded classes — same class is never loaded twice
- Packages themselves are never "loaded" — only individual classes are

---

## 9. Why is Java not 100% object-oriented?

Java has features that break pure OOP:

| Non-OOP Feature | Why it breaks OOP |
|---|---|
| Primitive types (`int`, `boolean`, etc.) | Not objects — no methods, no inheritance |
| `static` methods and fields | Belong to class, not an instance |
| `static` blocks | Execute without any object existing |
| `void` return type | Not an object |
| Operators (`+`, `-`, `*`) on primitives | Not method calls |

```java
int x = 10;                    // not an object
int sum = MathOps.add(5, 3);   // static call — no object needed
int[] arr = new int[5];
arr[0] = 1;                    // special syntax, not a method call
```

**Pure OOP languages** (Smalltalk, Ruby) — everything is an object, all operations are method calls.

---

## 10. What is Unicode and which system does Java use?

**Unicode** is a universal character encoding standard — assigns a unique number (code point) to every character across all writing systems.

Java uses **UTF-16** internally for `char` and `String`.

| Encoding | Used In Java |
|---|---|
| UTF-16 | Internal `char` / `String` representation |
| UTF-8 | File I/O, network communication |

```java
char a = 'A';           // Basic Latin — fits in one char
char heart = '\u2764';  // Unicode escape — heart symbol

String emoji = "😊";
System.out.println(emoji.length());              // 2 — surrogate pair (beyond BMP)
System.out.println(emoji.codePointCount(0, 2));  // 1 — one actual character
```

- Characters in BMP (0x0000–0xFFFF) fit in one `char`
- Characters beyond BMP (emoji, rare scripts) need **two** `char` values — called a surrogate pair

---

## 11. Which non-Unicode characters can start an identifier?

Valid **first characters** for a Java identifier:

| Character | Example |
|---|---|
| Letters A–Z, a–z | `counter`, `Name` |
| Underscore `_` | `_value`, `__temp` |
| Dollar sign `$` | `$generated` |
| Unicode letters from any language | `ñombre`, `世界` |

**Not allowed** as first character: digits (0–9), spaces, `@`, `#`, `-`

```java
int counter   = 1;   // valid
String _name  = "x"; // valid
boolean $flag = true; // valid
// int 1value = 0;   // invalid — starts with digit
// int my-var = 0;   // invalid — hyphen not allowed
```

**Gotcha**: Identifiers cannot be Java keywords (`if`, `class`, `while`) or literals (`true`, `false`, `null`).

---

## 12. What are primitive types and their wrapper classes?

| Primitive | Wrapper | Parse Method |
|---|---|---|
| `byte` | `Byte` | `Byte.parseByte()` |
| `short` | `Short` | `Short.parseShort()` |
| `int` | `Integer` | `Integer.parseInt()` |
| `long` | `Long` | `Long.parseLong()` |
| `float` | `Float` | `Float.parseFloat()` |
| `double` | `Double` | `Double.parseDouble()` |
| `char` | `Character` | — |
| `boolean` | `Boolean` | `Boolean.parseBoolean()` |

```java
int primitive = 42;
Integer wrapper = 42;       // autoboxing
Integer nullable = null;    // wrapper can be null
// int cannotBeNull = null; // compile error

// Wrappers required for collections
List<Integer> list = new ArrayList<>();
list.add(1); // autoboxing int → Integer
```

| Use | When |
|---|---|
| Primitive | Performance-critical, no null needed |
| Wrapper | Collections, generics, nullable values |

---

## 13. What methods are inside wrapper classes?

### Common across all wrappers

```java
Integer.valueOf(42);          // primitive → wrapper
Integer.parseInt("42");       // String → primitive
Integer.toString(42);         // primitive → String
Integer.compare(5, 10);       // compare two values
Integer.MAX_VALUE;            // constant
Integer.MIN_VALUE;            // constant
```

### Integer-specific

```java
Integer.toBinaryString(42);   // "101010"
Integer.toHexString(255);     // "ff"
Integer.toOctalString(8);     // "10"
Integer.parseInt("1A", 16);   // parse hex string → 26
```

### Double-specific

```java
Double.isNaN(0.0 / 0.0);      // true
Double.isInfinite(1.0 / 0.0); // true
Double.isFinite(3.14);        // true
```

### Character-specific

```java
Character.isDigit('5');       // true
Character.isLetter('A');      // true
Character.isUpperCase('A');   // true
Character.toLowerCase('A');   // 'a'
Character.toUpperCase('a');   // 'A'
```

### Boolean-specific

```java
Boolean.parseBoolean("true");         // true
Boolean.parseBoolean("yes");          // false — only "true" (case-insensitive) returns true
Boolean.logicalAnd(true, false);      // false
Boolean.logicalOr(true, false);       // true
```

---

## 14. What is autoboxing and unboxing?

**Autoboxing** — automatic conversion of primitive → wrapper object.
**Unboxing** — automatic conversion of wrapper object → primitive.

Introduced in Java 5.

```java
// Autoboxing — primitive to wrapper
Integer obj = 100;                    // same as Integer.valueOf(100)
List<Integer> list = new ArrayList<>();
list.add(1);                          // int autoboxed to Integer

// Unboxing — wrapper to primitive
int val = obj;                        // same as obj.intValue()
Integer a = 10, b = 20;
int sum = a + b;                      // both unboxed for arithmetic
```

### Common Gotchas

```java
// NullPointerException on unboxing null
Integer n = null;
int x = n;  // NullPointerException at runtime

// == comparison trap — Integer cache is only -128 to 127
Integer a = 127, b = 127;
System.out.println(a == b);  // true  — same cached object

Integer c = 128, d = 128;
System.out.println(c == d);  // false — different objects, use .equals()

// Performance — avoid autoboxing in tight loops
Integer sum = 0;
for (int i = 0; i < 1_000_000; i++) {
    sum += i; // unbox + add + autobox every iteration — slow
}
int primitiveSum = 0;
for (int i = 0; i < 1_000_000; i++) {
    primitiveSum += i; // no boxing — fast
}
```

### Wrapper Cache Ranges

| Wrapper | Cached Range |
|---|---|
| `Boolean` | `true` and `false` always cached |
| `Byte`, `Short`, `Integer`, `Long` | -128 to 127 |
| `Character` | 0 to 127 |
| `Float`, `Double` | Not cached |

---

## 15. What is casting — implicit and explicit?

### Primitive Casting

**Implicit (widening)** — smaller type → larger type, no data loss, automatic.

```
byte → short → int → long → float → double
```

**Explicit (narrowing)** — larger type → smaller type, possible data loss, requires cast operator.

```java
// Implicit — automatic, no data loss
int i = 100;
long l = i;       // int → long, automatic
double d = l;     // long → double, automatic

// Explicit — manual, possible data loss
double pi = 3.99;
int truncated = (int) pi;   // 3 — decimal part lost

int big = 130;
byte b = (byte) big;        // -126 — overflow, value wraps around
```

### Reference Casting

**Upcasting** — subclass → superclass, always safe, implicit.
**Downcasting** — superclass → subclass, requires explicit cast, can throw `ClassCastException`.

```java
class Animal { void sound() { System.out.println("..."); } }
class Dog extends Animal {
    void sound() { System.out.println("Woof"); }
    void fetch() { System.out.println("Fetching!"); }
}

// Upcasting — implicit, safe
Dog dog = new Dog();
Animal a = dog;       // Dog → Animal, automatic
a.sound();            // "Woof" — runtime polymorphism

// Downcasting — explicit, risky
Animal a2 = new Dog();
Dog d = (Dog) a2;     // Animal → Dog, explicit cast
d.fetch();            // works — a2 is actually a Dog

// Safe downcasting — always check with instanceof first
if (a2 instanceof Dog) {
    Dog safe = (Dog) a2;
    safe.fetch();
}

// Java 16+ pattern matching — cleaner
if (a2 instanceof Dog safedog) {
    safedog.fetch(); // no explicit cast needed
}
```

| | Implicit | Explicit |
|---|---|---|
| Also called | Widening / Upcasting | Narrowing / Downcasting |
| Data loss | No | Possible |
| Syntax | Automatic | `(TargetType) value` |
| Risk | None | `ClassCastException` for references |
