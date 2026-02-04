
# JVM Architecture & Execution Flow - Comprehensive Study Notes

## Table of Contents
1. [What is JVM?](#what-is-jvm)
2. [JVM Architecture Components](#jvm-architecture-components)
3. [Java Program Execution Flow](#java-program-execution-flow)
4. [Class Loader Subsystem](#class-loader-subsystem)
5. [Runtime Data Areas](#runtime-data-areas)
6. [Execution Engine](#execution-engine)
7. [Key Concepts & Comparisons](#key-concepts--comparisons)

---

## What is JVM?

**JVM (Java Virtual Machine)** is an abstract computing machine that enables the "Write Once, Run Anywhere" principle by converting platform-independent bytecode into machine-specific instructions.

**Key Points:**
- Not a physical machine, but a specification + implementation
- Provides runtime environment for Java bytecode
- Acts as intermediary between code and OS/CPU

---

## JVM Architecture Components

![JVM Architecture Diagram](./ss-jvm-architecture.png)


---

## Java Program Execution Flow

```
MyProgram.java (source code)
    ↓ [javac compiler]
    .class file (bytecode - platform independent)
    ↓ [JVM Class Loader]
    Loaded in Method Area (verified & validated)
    ↓ [Execution Engine]
    Machine Code (platform dependent)
    ↓ [OS + CPU]
    Program Execution
```

---

## Class Loader Subsystem

### Hierarchy & Delegation Model

| Class Loader | Parent | Loads From | Examples |
|---|---|---|---|
| **Bootstrap** | None (root) | `<JAVA_HOME>/jre/lib/rt.jar` | `java.lang.*`, `java.util.*` |
| **Extension** | Bootstrap | `<JAVA_HOME>/jre/lib/ext/` | `javax.crypto.*`, `javax.swing.*` |
| **Application** | Extension | Project Classpath | User-defined classes |

**Delegation Flow:** Application → Extension → Bootstrap (ask parent first, load if not found)

### Three Phases of Class Loading

#### 1. **Loading**
```
Read .class file → Create Class object → Store in Method Area
```

#### 2. **Linking** (3 sub-phases)

| Sub-phase | Purpose | Example |
|---|---|---|
| **Verification** | Ensure bytecode validity & security | Check for stack overflow/underflow |
| **Preparation** | Allocate memory for static variables | `static int x;` gets default value `0` |
| **Resolution** | Convert symbolic references → direct references | `System.out.println()` → memory address |

#### 3. **Initialization**
```
Assign explicit values to static variables → Execute static blocks
```

### Example: Class Loading Execution

```java
public class Test {
    static int x = 10;
    static { System.out.println("Static block!"); }
    
    public static void main(String[] args) {
    System.out.println("Main method");
    }
}
```

**Execution Sequence:**
1. Application Loader finds `Test.class`
2. **Load**: Class object created
3. **Link**: `x` prepared with default `0`, references resolved
4. **Init**: `x` assigned `10`, static block executes → **prints "Static block!"**
5. `main()` executes → **prints "Main method"**

---

## Runtime Data Areas

### Memory Layout

```
┌──────────────────────────────────────────┐
│  JVM Memory (Heap) - Shared              │
├──────────────────────────────────────────┤
│  Method Area                             │
│  • Class metadata, static variables      │
│  • Method bytecode, constants            │
├──────────────────────────────────────────┤
│  Heap Area                               │
│  • All object instances                  │
│  • Instance variables                    │
│  ★ Managed by Garbage Collector          │
├──────────────────────────────────────────┤
│  Per-Thread Memory                       │
│  ├─ Stack Area                           │
│  │  • Local variables, method frames     │
│  │  • Intermediate calculations          │
│  ├─ PC Register                          │
│  │  • Current instruction address        │
│  └─ Native Method Stack                  │
│     • Native code execution              │
└──────────────────────────────────────────┘
```

### Detailed Comparison

| Area | Scope | Thread Shared? | Lifetime | GC? | Use |
|---|---|---|---|---|---|
| **Method Area** | Class-level | Yes (1 per JVM) | JVM lifetime | No | Store class definitions |
| **Heap** | Object-level | Yes (1 per JVM) | Object lifetime | **Yes** | Store objects |
| **Stack** | Method-level | No (per thread) | Method lifetime | No | Store local variables |
| **PC Register** | Instruction-level | No (per thread) | Thread lifetime | No | Track execution |
| **Native Stack** | Native code | No (per thread) | Thread lifetime | No | Support C/C++ calls |

### Stack Frame Example

```
When method is called:
┌────────────────────────┐
│ Stack Frame (method X) │
├────────────────────────┤
│ Local Variables        │  ← method parameters, local vars
├────────────────────────┤
│ Operand Stack          │  ← intermediate results
├────────────────────────┤
│ Dynamic Linking        │  ← runtime constant pool refs
└────────────────────────┘
Frame destroyed when method returns
```

---

## Execution Engine

### Component Comparison

| Component | How It Works | Pros | Cons | Use Case |
|---|---|---|---|---|
| **Interpreter** | Reads bytecode line-by-line, executes | Simple, fast startup | Slower execution | Every instruction initially |
| **JIT Compiler** | Compiles hotspot bytecode → machine code | Faster execution | Slower startup, more memory | Frequently used code |
| **Garbage Collector** | Removes unreferenced objects from Heap | Automatic memory management | Pause times, overhead | Memory cleanup |

### Interpreter vs JIT Timing

```
First 10,000 executions (example):
│ Interpreter    ████████████████████ (slower but simple)
│ JIT Compiler   ████████ (fast after compilation)
│
└─► JVM automatically switches to JIT for hot code
```

### Garbage Collection Process

```
1. MARK phase
   ├─ Start from GC roots (threads, static refs)
   └─ Traverse object graph, mark reachable objects

2. SWEEP phase
   ├─ Scan entire heap
   └─ Free unmarked (unreachable) objects

3. COMPACT phase (optional)
   ├─ Move surviving objects together
   └─ Reduce memory fragmentation
```

---

## Key Concepts & Comparisons

### Code Transformation Levels

```
┌─────────────────────┐
│   Source Code       │  .java file
│  (Human language)   │  Java, C++, Python
└──────────┬──────────┘
       │ [Compiler - javac]
       ↓
┌─────────────────────┐
│   Bytecode          │  .class file
│ (Platform-neutral)  │  Works on any JVM
└──────────┬──────────┘
       │ [JVM Execution Engine]
       ↓
┌─────────────────────┐
│   Machine Code      │  Binary instructions
│ (Platform-specific) │  CPU understands
└─────────────────────┘
```

### JVM vs JRE vs JDK

| Component | Includes | Purpose |
|---|---|---|
| **JVM** | Bytecode executor | Runtime execution |
| **JRE** | JVM + libraries | Run Java programs |
| **JDK** | JRE + compiler + tools | Develop Java programs |

### Stack vs Heap

| Feature | Stack | Heap |
|---|---|---|
| **Speed** | Faster (LIFO) | Slower (GC managed) |
| **Memory Size** | Smaller (MB range) | Larger (GB range) |
| **Thread Access** | Thread-local | Shared by all threads |
| **Allocation** | Automatic (LIFO) | Automatic (GC) |
| **Exception** | StackOverflowError | OutOfMemoryError |
| **Example** | `int x = 5;` | `new Student()` |

---

## Benefits of JVM Architecture

| Benefit | How It Works | Value |
|---|---|---|
| **Portability (WORA)** | Same bytecode runs everywhere | Write once, deploy anywhere |
| **Performance** | JIT compiles hot code to machine code | Near-native speed for production code |
| **Security** | Bytecode verification, sandboxing | Prevents malicious/invalid code execution |
| **Memory Safety** | Automatic GC, bounds checking | No buffer overflows or memory leaks |
| **Multithreading** | Per-thread stack & PC register | Safe concurrent execution |
| **Dynamic Loading** | Classes loaded on-demand | Lower startup memory usage |

---

## Interview Quick Reference

**Q: What happens when you run `java MyClass`?**
1. JVM starts
2. Application Class Loader finds MyClass.class
3. Class loaded → linked → initialized
4. main() method invoked
5. Execution Engine executes bytecode
6. GC cleans unused objects
7. JVM shuts down

**Q: Why is Java platform-independent but JVM is not?**
- Bytecode is platform-independent (same everywhere)
- JVM implementation is platform-specific (different for Windows, Linux, Mac)
- JVM abstracts platform differences

**Q: Stack vs Heap memory?**
- **Stack**: Thread-local, fixed size, fast allocation, automatic cleanup
- **Heap**: Shared, dynamic size, slower allocation, GC cleanup

---

