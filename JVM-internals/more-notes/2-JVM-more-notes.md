
>refer 
> https://youtu.be/kK5ep_dd798?si=c3GSWl06jam4OLSn

<br/>

# 📘 JVM Architecture & Execution Flow – Notes

## 1. What is JVM?

* **JVM (Java Virtual Machine)** is an abstract computing machine that enables Java programs to run on any platform (Write Once, Run Anywhere).
* It provides a runtime environment for Java bytecode.
* It **converts platform-independent bytecode into machine-specific instructions**.

👉 JVM is not a physical machine, but a **specification + implementation**.

---

## 2. Components of JVM Architecture

### **(A) Class Loader Subsystem**

* **Job**: Loads `.class` files into memory.
* **Why needed**: Without loading, JVM cannot know what code to run.
* **Steps**:

  1. **Loading** – Finds and loads `.class` file.
  2. **Linking** – Verifies bytecode, prepares memory for static variables.
  3. **Initialization** – Initializes static variables and blocks.

---

### **(B) Runtime Data Areas (Memory Management)**

* **Job**: Provides memory to execute program.
* **Why needed**: JVM needs separate areas to manage program data.

**1. Method Area**

* Stores class-level data (metadata, static variables, method code).
* Shared by all threads.

**2. Heap Area**

* Stores objects & instance variables.
* Garbage Collected (managed by **Garbage Collector**).

**3. Stack Area**

* Each thread has its own **Java Stack**.
* Stores **frames** → each frame has local variables, operand stack, partial results.
* Lifecycle tied to thread lifecycle.

**4. PC Register (Program Counter)**

* Each thread has its own PC register.
* Stores the address of currently executing JVM instruction.

**5. Native Method Stack**

* Supports execution of **native (non-Java)** methods written in C/C++.
* Used via JNI (Java Native Interface).

---

### **(C) Execution Engine**

* **Job**: Executes bytecode instructions.
* **Why needed**: Converts intermediate bytecode into actual machine code.

**Components**:

1. **Interpreter**

   * Reads bytecode **line by line** and executes.
   * Simple, but slower.

2. **JIT Compiler (Just-In-Time)**

   * Converts **hotspot bytecode (frequently executed code)** into native machine code.
   * Improves performance drastically.

3. **Garbage Collector (GC)**

   * Automatically frees memory of unused objects in Heap.
   * Prevents memory leaks.

---

### **(D) Native Method Interface (JNI)**

* Bridge between Java and other languages (like C, C++).
* Needed when interacting with system resources.

---

### **(E) Native Method Libraries**

* Libraries required for native methods execution.

---

## 3. Flow of Java Program Execution

1. **Programmer writes source code** → `MyProgram.java`
2. **Compiler (javac)** compiles → `.class` file (Bytecode)

   * Platform independent
3. **JVM Class Loader** loads `.class` file
4. **Bytecode Verified** for security & correctness
5. **Execution Engine** interprets or compiles bytecode into **Machine Code**
6. **OS + CPU** executes the native instructions

---

## 4. Life Cycle of a Java Program

1. **Write** → `.java` file (source code, human-readable, **high-level code**)
2. **Compile** → `.class` file (bytecode, platform-independent, **intermediate code**)
3. **Load & Verify** → JVM class loader + verifier
4. **Execute** → Interpreter / JIT → **machine code** (platform-dependent, **low-level code**)
5. **Runtime Management** → Memory allocation, Garbage Collection
6. **Program End** → JVM shutdown, memory cleanup

---

## 5. Key Terminologies

* **Source Code (.java file)** – Human-readable high-level code.
* **Bytecode (.class file)** – Intermediate representation understood by JVM, platform independent.
* **Interpreter** – Executes bytecode **line by line**.
* **Compiler (javac)** – Converts `.java` → `.class`.
* **JIT Compiler** – Converts **frequently used bytecode** into machine code for faster execution.
* **Machine Code** – Binary instructions understood by CPU.
* **High-Level Language** – Java, C++, Python (closer to human language).
* **Low-Level Language** – Assembly, Machine Code (closer to hardware).

---

## 6. Why This Architecture is Useful?

* **Portability** → Java runs anywhere (bytecode + JVM).
* **Performance** → JIT compiler optimizes runtime execution.
* **Security** → Bytecode verification prevents malicious code.
* **Memory Management** → Garbage Collector avoids manual memory handling.
* **Multithreading Support** → Separate stacks, PC registers for each thread.

---

<br/><br/><br/>




## 📘 JVM Class Loader Subsystem – Notes

The **Class Loader Subsystem** in JVM is responsible for **loading, linking, and initializing classes** when a Java program runs.

---

## 1. **Class Loader Hierarchy**

### 🔹 **Bootstrap Class Loader**

* **Parent of all class loaders**.
* Loads **core Java classes** (part of Java Standard Library).
* Classes found in **`<JAVA_HOME>/jre/lib/rt.jar`** or modules in modern Java.
* Implemented in **native code** (C/C++).
* Has **no parent** (top of hierarchy).
* **Example**: `java.lang.String`, `java.util.ArrayList`.

---

### 🔹 **Extension (Platform) Class Loader**

* Child of Bootstrap Class Loader.
* Loads **extension libraries** located in:

  * `<JAVA_HOME>/jre/lib/ext/`
  * or directories specified by `java.ext.dirs` system property.
* Loads classes like **security providers, JAXP, JCE, javax.* libraries*\*.
* **Example**: `javax.crypto.*`, `javax.swing.*`.

---

### 🔹 **Application (System) Class Loader**

* Child of Extension Class Loader.
* **Default class loader** in Java.
* Loads **application-specific classes** from:

  * Classpath (`CLASSPATH` environment variable).
  * JAR files in `lib/` folder of the project.
* Most user-defined classes are loaded here.
* **Example**: If you create `MyClass.class`, this loader loads it.

---

📌 **Hierarchy order:**
`Bootstrap Class Loader → Extension Class Loader → Application Class Loader`
👉 Follows **Delegation Model** (first ask parent; if not found, load itself).

---

## 2. **Class Loader Subsystem Phases**

### **A. Loading**

* Class loader reads the `.class` file (bytecode) into JVM memory.
* Creates a **Class object** (java.lang.Class) in the Method Area.

---

### **B. Linking**

Performed after class is loaded. Involves 3 sub-steps:

1. **Verification** ✅

   * Ensures bytecode follows JVM rules.
   * Prevents illegal memory access, security issues.
   * Example: Ensures stack underflow/overflow won’t occur.

2. **Preparation** 📦

   * Allocates memory for **static variables**.
   * Assigns default values (`0`, `null`, `false`).

3. **Resolution** 🔗

   * Converts **symbolic references** (like method names, field names) into **direct references** in memory.
   * Example: Linking `System.out.println()` to its actual memory address.

---

### **C. Initialization**

* Happens after loading and linking.
* Static variables are assigned **explicit values** (if provided).
* Static blocks (`static {}`) are executed.
* This phase ensures the class is fully ready before being used.

---

## 3. **Flow Example**

Let’s say you run:

```java
public class Test {
    static int x = 10;
    static { System.out.println("Class Loaded!"); }
    public static void main(String[] args) {
        System.out.println("Hello World");
    }
}
```

**Execution flow:**

1. `Test.class` is **loaded** by Application Class Loader.
2. JVM **verifies** bytecode, prepares `x` with default value `0`, resolves references.
3. JVM **initializes** → assigns `x=10`, runs static block → prints `"Class Loaded!"`.
4. Executes `main()` → prints `"Hello World"`.

---

## 4. **Why is Class Loader Subsystem Important?**

* Enables **modularity** (different loaders can load different parts of an application).
* Provides **security** (prevents loading of malicious/unverified classes).
* Allows **custom class loaders** (for frameworks like Spring, Tomcat).
* Supports **dynamic loading** (classes are loaded only when required, saving memory).

---

<br/><br/><br/>



## 📘 JVM Runtime Data Areas & Execution Engine – Notes

When a Java program runs, the JVM creates and manages **runtime memory areas** where data and instructions are stored and executed.

---

## 1. **JVM Runtime Data Areas**

### 🔹 **Method Area**

* Stores **class-level data**:

  * Method code
  * Constants
  * Metadata about classes
* Keeps class definitions after loading.
* Shared by all threads.
* Example: Information about `class Test { ... }` after it is loaded.

---

### 🔹 **Heap Area**

* Stores **all objects** and their **instance variables**.
* Created at JVM startup, shared among all threads.
* **Static variables** are also stored in Heap.
* Garbage Collector works here.
* Example: `new Student("John")` object lives in Heap.

---

### 🔹 **Stack Area**

* Each thread has its **own stack**.
* Stores:

  * Local variables
  * Method call details (stack frames)
  * Intermediate results
* Every method invocation creates a **stack frame**.
* Destroyed when method exits.
* Example: Method parameters and local variables live here.

---

### 🔹 **Program Counter (PC) Register**

* Each thread has its own PC register.
* Stores the **address of the next instruction** to execute.
* Helps JVM execute instructions sequentially in a thread.

---

### 🔹 **Native Method Stack**

* Used when Java interacts with **native code (C/C++)**.
* Supports execution of system-level or OS-dependent tasks.
* Example: File handling, network calls through **JNI (Java Native Interface)**.

---

## 2. **Execution Engine**

The Execution Engine is responsible for **running bytecode** inside the JVM.

### **A. Interpreter**

* Reads bytecode **line-by-line** and executes.
* **Pros:** Simple implementation.
* **Cons:** Slower, since each instruction is executed individually.

### **B. JIT Compiler (Just-In-Time)**

* Compiles **frequently executed bytecode** (called **hotspots**) into **native machine code**.
* Runs faster than interpretation.
* Performs optimizations for performance.
* Example: If a loop runs thousands of times, JIT compiles it into machine code for speed.

---

## 3. **Garbage Collection (GC)**

* Automatic memory management in JVM.
* Frees memory by removing **unused objects** from Heap.
* Ensures memory is not exhausted.
* **Steps:**

  1. GC scans Heap for unreachable objects.
  2. Frees memory occupied by those objects.
  3. Compacts Heap to avoid fragmentation.
* Example:

  ```java
  Student s = new Student("John");
  s = null; // object becomes eligible for GC
  ```

---

## 4. **Handling Native Code (JNI)**

* **JNI (Java Native Interface)** allows Java code to call native libraries.
* Useful when:

  * Accessing OS-level resources
  * Using existing legacy C/C++ libraries
* Example: Database drivers, device drivers.

---

## 5. **Summary Flow**

1. **Class loaded** into Method Area.
2. **Objects created** in Heap.
3. **Method calls** → Stack Frames created in Stack.
4. **Instructions executed** using PC Register.
5. **If native code required** → Native Method Stack via JNI.
6. **Execution Engine** (Interpreter + JIT) runs the code.
7. **GC cleans Heap** when objects are unused.

---



<br/><br/><br/>


## 📌 JVM Summary Notes

* **Class Loader** 🏗️
  Loads `.class` bytecode files into JVM memory.
  Handles **loading → linking → initialization**.

* **Runtime Data Areas** 💾

  * **Heap** → Stores objects & static variables.
  * **Stack** → Stores local variables & method calls (per thread).
  * **Method Area** → Stores class-level data, constants, metadata.
  * **PC Register** → Holds address of next instruction (per thread).
  * **Native Method Stack** → Supports execution of C/C++ native code.

* **Execution Engine** ⚙️

  * **Interpreter** → Converts bytecode **line by line** → slower.
  * **JIT Compiler** → Converts **hotspot code** into native machine code → faster.
  * Works together: interpreter for general execution, JIT for performance-critical parts.

* **Garbage Collector (GC)** 🗑️
  Automatically **reclaims Heap memory** of unreferenced objects.
  Prevents memory leaks & improves efficiency.

* **Native Methods (JNI)** 🌐
  Provides interface to interact with **non-Java code** (C/C++, system libraries).

* **Linking Phase** 🔗

  * **Verification**: Ensures bytecode validity.
  * **Preparation**: Allocates memory for static variables.
  * **Resolution**: Replaces symbolic references with actual memory addresses.

---


<br/><br/><br/><br/>



## 📘 What More to Learn Beyond JVM Basics

## 1. **Java Program Execution in Depth**

* **JDK vs JRE vs JVM** – Differences and roles.
* **javac, java, javap, javadoc** tools.
* **ClassLoader types** (Bootstrap, Extension, Application).
* **Bytecode structure** (`javap -c` to decompile & analyze).

---

## 2. **Memory Management**

* **Stack vs Heap memory** differences.
* **Garbage Collection (GC)**

  * Algorithms (Mark & Sweep, Generational GC, G1GC, ZGC).
  * Minor GC vs Major GC vs Full GC.
* **Memory leaks** in Java (how they happen even with GC).

---

## 3. **Execution Engine Internals**

* **Interpreter vs JIT** → When JVM uses which.
* **HotSpot JVM** – The most widely used JVM implementation.
* **Adaptive Optimization** – JVM optimizes code at runtime.

---

## 4. **Java Program Lifecycle Management**

* **Class initialization order** (static blocks, constructors).
* **Thread lifecycle** and how JVM handles multiple threads.
* **Daemon threads** (GC runs as a daemon).

---

## 5. **Key Concepts Around JVM**

* **Reflection API** – How JVM loads metadata at runtime.
* **Annotations processing** – How JVM/JDK tools read them.
* **JVM Languages** – Scala, Kotlin, Groovy also run on JVM.

---

## 6. **Performance & Monitoring**

* **JVM tuning parameters** (`-Xmx`, `-Xms`, `-XX:+UseG1GC`).
* **Thread dumps & Heap dumps** – for debugging issues.
* **JConsole, VisualVM** – Tools to monitor JVM.

---

## 7. **Advanced Terminologies** (must know for strong base)

* **Managed vs Unmanaged memory**.
* **Just-In-Time compilation (C1, C2 compilers)**.
* **Native code execution** via JNI.
* **Ahead-of-Time (AOT) compilation** (new in Java 9+).
* **Bytecode instrumentation** (used in profiling tools).

---

## 8. **Interview-Oriented JVM Topics**

* Why is Java **platform independent** but JVM is platform dependent?
* Difference between **JVM, JRE, JDK**.
* What is **ClassLoader** & how many types exist?
* Explain **GC process** in simple terms.
* Stack vs Heap memory in JVM.
* Difference between **Interpreter and Compiler**.
* How does JIT improve performance?
* What happens when you run `java MyClass` step by step.

---



<br/><br/><br/>

## 🏗️ JVM Memory Spaces Recap

### **Metaspace vs PermGen**

* **PermGen (Pre-Java 8)**

  * Stored class metadata, methods, and interned Strings.
  * Fixed size, part of **JVM heap**.
  * Could cause **OutOfMemoryError: PermGen space** if too many classes loaded.
  * Difficult to tune in dynamic class-loading environments (e.g., app servers, frameworks).

* **Metaspace (Java 8 onwards)**

  * Replaced PermGen.
  * Allocated in **native (OS) memory**, not heap.
  * Grows dynamically, limited by **system memory** unless capped with `-XX:MaxMetaspaceSize`.
  * Problems:

    * If too many classes are loaded (e.g., frameworks with proxies, dynamic code generation), can still cause **server crash** due to native memory exhaustion.
    * Needs monitoring.

---

## 🗑️ Garbage Collection (GC) Algorithms

### **1. Serial GC**

* Single-threaded collection.
* Best for small apps or client machines.
* Causes **stop-the-world pauses**.

### **2. Parallel GC (a.k.a. Throughput Collector)**

* Multi-threaded, uses multiple CPU cores.
* Suitable for large heaps, batch jobs.
* Still has stop-the-world pauses.
* Variants:

  * **Parallel New GC** – parallel for young gen.
  * **Parallel Old GC** – also parallel for old gen.

### **3. CMS (Concurrent Mark-Sweep)**

* Reduces pause time by doing GC concurrently with application threads.
* Old generation collected without long pauses.
* Drawbacks:

  * High CPU usage.
  * Fragmentation issues (can still cause Full GC).
* Deprecated since JDK 9.

### **4. G1 GC (Garbage First)**

* Default GC in Java 9+.
* Divides heap into equal-sized **regions** (no strict Young/Old boundaries).
* Prioritizes collecting regions with the most garbage.
* Predictable pause times (`-XX:MaxGCPauseMillis`).
* Great for **large heaps & server apps**.

---

## ⚠️ Problems with GC

* **Efficiency challenges**: identifying live vs. garbage objects correctly.
* **Stop-the-world pauses**: application halts during GC.
* **OOM Errors**: if heap or Metaspace not tuned correctly.
* **Fragmentation** (esp. CMS): memory scattered, can’t allocate large objects.

---

## ⚙️ JVM GC Tuning & Best Practices

1. **Monitor GC logs**

   * Use `-Xlog:gc*` (Java 9+) or `-verbose:gc` (pre-Java 9).
   * Track frequency & duration of collections.

2. **Choose right GC**

   * Small app → Serial GC.
   * Multi-threaded/batch jobs → Parallel GC.
   * Low latency services → G1 GC (or ZGC/Shenandoah in latest JDKs).

3. **Heap Tuning**

   * `-Xms` and `-Xmx` to set min/max heap.
   * Avoid frequent heap resizing.

4. **Metaspace Tuning**

   * Use `-XX:MaxMetaspaceSize` to cap memory use.
   * Monitor classloading with `jcmd VM.classloaders`.

5. **Application-level Best Practices**

   * Avoid classloader leaks (e.g., redeploy in app servers).
   * Release references to unused objects.
   * Use object pooling cautiously (may cause memory retention).
   * Prefer immutable objects (shorter lifespan).

---

✅ So in short:

* **PermGen → Metaspace** migration was done to fix fixed-size issues.
* **GC choices** depend on app type (small app, throughput-heavy, low-latency).
* **Problems**: OOM, fragmentation, pauses.
* **Best practices**: monitor, tune heap/metaspace, choose right GC, avoid memory leaks.

---

