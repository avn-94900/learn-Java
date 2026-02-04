# Java Memory Management - Interview Questions Bank

## 🎯 Question Categories Overview

This comprehensive question bank covers all aspects of Java memory management, categorized by difficulty and topic area.

**Difficulty Levels:**
- 🟢 **Beginner** (0-2 years experience)
- 🟡 **Intermediate** (2-5 years experience) 
- 🔴 **Advanced** (5+ years experience)

---

## 📚 Table of Contents

1. [JVM Memory Architecture](#1-jvm-memory-architecture)
2. [Heap Memory](#2-heap-memory)
3. [Stack Memory](#3-stack-memory)
4. [Method Area & Metaspace](#4-method-area--metaspace)
5. [Garbage Collection](#5-garbage-collection)
6. [GC Algorithms](#6-gc-algorithms)
7. [Memory Tuning & Performance](#7-memory-tuning--performance)
8. [Troubleshooting & Errors](#8-troubleshooting--errors)
9. [Code Analysis Questions](#9-code-analysis-questions)
10. [Scenario-Based Questions](#10-scenario-based-questions)

---
| # | Question | Difficulty | Topic |
|---|----------|-----------|-------|
| **1. JVM Memory Architecture** | | | |
| 1.1 | What are the main memory areas in JVM? | 🟢 Beginner | Basic Concepts |
| 1.2 | What is the difference between Heap and Stack memory? | 🟢 Beginner | Basic Concepts |
| 1.3 | Can you store objects in Stack memory? | 🟢 Beginner | Basic Concepts |
| 1.4 | How do Stack and Heap interact with each other? | 🟡 Intermediate | Memory Relationships |
| 1.5 | What happens to Stack and Heap when a method completes execution? | 🟡 Intermediate | Memory Relationships |
| 1.6 | Explain the memory layout of a Java process in detail. | 🔴 Advanced | Advanced Architecture |
| 1.7 | How does JVM decide memory allocation between different areas? | 🔴 Advanced | Advanced Architecture |
| **2. Heap Memory** | | | |
| 2.1 | What are the different generations in Heap memory? | 🟢 Beginner | Heap Structure |
| 2.2 | Where are new objects created in Heap? | 🟢 Beginner | Heap Structure |
| 2.3 | What is the purpose of Survivor spaces (S0 and S1)? | 🟢 Beginner | Heap Structure |
| 2.4 | Explain the lifecycle of an object from creation to garbage collection. | 🟡 Intermediate | Object Lifecycle |
| 2.5 | When does an object get promoted from Young to Old Generation? | 🟡 Intermediate | Object Lifecycle |
| 2.6 | What happens if Eden space fills up? | 🟡 Intermediate | Object Lifecycle |
| 2.7 | How does heap memory allocation work internally? | 🔴 Advanced | Advanced Heap Concepts |
| 2.8 | What are Humongous Objects in G1GC context? | 🔴 Advanced | Advanced Heap Concepts |
| **3. Stack Memory** | | | |
| 3.1 | What is stored in Stack memory? | 🟢 Beginner | Stack Basics |
| 3.2 | Is Stack memory shared between threads? | 🟢 Beginner | Stack Basics |
| 3.3 | What is a Stack Frame? | 🟢 Beginner | Stack Basics |
| 3.4 | How does method invocation work with Stack? | 🟡 Intermediate | Stack Operations |
| 3.5 | What happens to local variables when a method exits? | 🟡 Intermediate | Stack Operations |
| 3.6 | What causes StackOverflowError and how to fix it? | 🔴 Advanced | Stack Performance & Issues |
| 3.7 | How does Stack size affect application performance? | 🔴 Advanced | Stack Performance & Issues |
| **4. Method Area & Metaspace** | | | |
| 4.1 | What is Method Area and what does it store? | 🟢 Beginner | Method Area Basics |
| 4.2 | What is the difference between PermGen and Metaspace? | 🟢 Beginner | Method Area Basics |
| 4.3 | Where are static variables stored and when are they initialized? | 🟡 Intermediate | Static Variables & Methods |
| 4.4 | How do static variables behave with respect to memory and threads? | 🟡 Intermediate | Static Variables & Methods |
| 4.5 | What causes OutOfMemoryError: Metaspace? | 🔴 Advanced | Class Loading & Metaspace |
| 4.6 | How does class unloading work in Metaspace? | 🔴 Advanced | Class Loading & Metaspace |
| **5. Garbage Collection** | | | |
| 5.1 | What is Garbage Collection and why is it needed? | 🟢 Beginner | GC Fundamentals |
| 5.2 | What makes an object eligible for Garbage Collection? | 🟢 Beginner | GC Fundamentals |
| 5.3 | What are the different types of Garbage Collection? | 🟢 Beginner | GC Fundamentals |
| 5.4 | Explain the Mark and Sweep algorithm. | 🟡 Intermediate | GC Process & Algorithms |
| 5.5 | What are GC roots and why are they important? | 🟡 Intermediate | GC Process & Algorithms |
| 5.6 | How does Generational Garbage Collection work? | 🟡 Intermediate | GC Process & Algorithms |
| 5.7 | What is the difference between concurrent and parallel garbage collection? | 🔴 Advanced | Advanced GC Concepts |
| 5.8 | Explain the concept of GC ergonomics. | 🔴 Advanced | Advanced GC Concepts |
| **6. GC Algorithms** | | | |
| 6.1 | When would you use Serial GC? | 🟢 Beginner | Serial & Parallel GC |
| 6.2 | What is the difference between Parallel GC and Parallel Old GC? | 🟢 Beginner | Serial & Parallel GC |
| 6.3 | How does CMS GC work and what are its phases? | 🟡 Intermediate | Concurrent Mark Sweep |
| 6.4 | What problems can occur with CMS GC? | 🟡 Intermediate | Concurrent Mark Sweep |
| 6.5 | How does G1GC differ from traditional generational collectors? | 🟡 Intermediate | G1 Garbage Collector |
| 6.6 | What is the G1GC evacuation process? | 🟡 Intermediate | G1 Garbage Collector |
| 6.7 | What are the key features of ZGC? | 🔴 Advanced | Low-Latency Collectors |
| 6.8 | When should you consider using Shenandoah GC? | 🔴 Advanced | Low-Latency Collectors |
| **7. Memory Tuning & Performance** | | | |
| 7.1 | What are the most important JVM memory tuning parameters? | 🟢 Beginner | JVM Parameters |
| 7.2 | How do you calculate appropriate heap size for an application? | 🟢 Beginner | JVM Parameters |
| 7.3 | What tools can you use to monitor Java memory usage? | 🟡 Intermediate | Performance Monitoring |
| 7.4 | How do you analyze GC logs for performance issues? | 🟡 Intermediate | Performance Monitoring |
| 7.5 | What are the best practices for minimizing GC pressure? | 🔴 Advanced | Memory Optimization |
| 7.6 | How do you handle memory leaks in Java applications? | 🔴 Advanced | Memory Optimization |
| **8. Troubleshooting & Errors** | | | |
| 8.1 | What are the different types of OutOfMemoryError? | 🟢 Beginner | OutOfMemoryError Variants |
| 8.2 | What causes StackOverflowError and how to debug it? | 🟢 Beginner | OutOfMemoryError Variants |



## 1. JVM Memory Architecture

### 1.1 Basic Concepts 🟢

**Q1.1:** What are the main memory areas in JVM?



- **Heap Memory**: Stores all objects and arrays (shared)
- **Stack Memory**: Method calls, local variables (per thread)
- **Method Area/Metaspace**: Class metadata, static variables
- **PC Registers**: Program counter for each thread
- **Native Method Stacks**: For JNI calls
</details>

**Q1.2:** What is the difference between Heap and Stack memory?



| Aspect | Heap | Stack |
|--------|------|-------|
| **Scope** | Shared across all threads | Per thread |
| **Stores** | Objects, arrays, instance variables | Method calls, local variables, references |
| **Speed** | Slower (managed by GC) | Faster (LIFO structure) |
| **Size** | Larger, configurable | Smaller, per thread |
| **Management** | Automatic (GC) | Automatic (method scope) |
</details>

**Q1.3:** Can you store objects in Stack memory?



**No.** Stack can only store:
- Primitive variables (int, double, etc.)
- Object references (pointers to heap objects)
- Method call information

Objects themselves are always created in Heap memory.
</details>

### 1.2 Memory Relationships 🟡

**Q1.4:** How do Stack and Heap interact with each other?



```java
public void example() {
    int primitive = 42;              // Stack: stores value directly
    String reference = "Hello";      // Stack: stores reference → Heap: stores object
    List<String> list = new ArrayList<>(); // Stack: reference → Heap: ArrayList object
}
```
Stack holds references that point to objects in Heap.
</details>

**Q1.5:** What happens to Stack and Heap when a method completes execution?



- **Stack**: Method's stack frame is immediately removed (LIFO)
- **Heap**: Objects remain until garbage collected
- **References**: Lost when stack frame is removed, making objects eligible for GC
</details>

### 1.3 Advanced Architecture 🔴

**Q1.6:** Explain the memory layout of a Java process in detail.



```
Process Memory Layout:
┌─────────────────┐
│   Heap Memory   │ ← Objects, arrays (-Xmx controls size)
├─────────────────┤
│   Metaspace     │ ← Class metadata (native memory)
├─────────────────┤
│ Thread Stacks   │ ← One per thread (-Xss controls size)
├─────────────────┤
│ Direct Memory   │ ← NIO, off-heap allocations
├─────────────────┤
│ Code Cache      │ ← Compiled native code (JIT)
└─────────────────┘
```
</details>

**Q1.7:** How does JVM decide memory allocation between different areas?



- **Heap**: Explicitly set via -Xms/-Xmx
- **Stack**: Per thread, set via -Xss (default ~1MB)
- **Metaspace**: Dynamic, grows as needed (native memory)
- **Direct Memory**: Limited by available system memory
- **Code Cache**: JIT compiler optimizations (-XX:ReservedCodeCacheSize)
</details>

---

## 2. Heap Memory

### 2.1 Heap Structure 🟢

**Q2.1:** What are the different generations in Heap memory?



1. **Young Generation**:
   - Eden Space (new objects)
   - Survivor 0 (S0) 
   - Survivor 1 (S1)

2. **Old Generation** (Tenured):
   - Long-lived objects promoted from Young Gen

**Default Ratio**: Young:Old ≈ 1:2
</details>

**Q2.2:** Where are new objects created in Heap?



**Eden Space** (part of Young Generation). All new objects start here, except for very large objects which may go directly to Old Generation.
</details>

**Q2.3:** What is the purpose of Survivor spaces (S0 and S1)?



- Hold objects that survived at least one Minor GC
- **Only one is active** at a time
- Objects move between S0 ↔ S1 during GC cycles
- After surviving 8-15 cycles, objects are promoted to Old Generation
</details>

### 2.2 Object Lifecycle 🟡

**Q2.4:** Explain the lifecycle of an object from creation to garbage collection.



```
Object Lifecycle:
New Object → Eden Space → Minor GC → Survivor Space (S0/S1) 
→ Multiple GC Cycles → Old Generation → Major GC → Collected
```

1. **Creation**: Object allocated in Eden
2. **First GC**: If reachable, moves to Survivor space
3. **Subsequent GCs**: Moves between S0 ↔ S1
4. **Promotion**: After 8-15 GC cycles, moves to Old Generation
5. **Collection**: Eventually garbage collected when unreachable
</details>

**Q2.5:** When does an object get promoted from Young to Old Generation?



**Conditions for promotion:**
1. Object survives multiple Minor GC cycles (default: 15)
2. Survivor space is full (premature promotion)
3. Object is too large for Survivor space
4. JVM optimization decisions

**Parameter**: `-XX:MaxTenuringThreshold=15`
</details>

**Q2.6:** What happens if Eden space fills up?



**Minor GC is triggered:**
1. All application threads are paused (Stop-the-World)
2. Reachable objects in Eden are copied to Survivor space
3. Eden space is cleared
4. Active Survivor space is swapped (S0 ↔ S1)
5. Application threads resume
</details>

### 2.3 Advanced Heap Concepts 🔴

**Q2.7:** How does heap memory allocation work internally?



**TLAB (Thread Local Allocation Buffers)**:
- Each thread gets a small portion of Eden space
- Reduces synchronization overhead for object allocation
- When TLAB fills up, thread requests new TLAB
- Very large objects bypass TLAB and allocate directly

**Allocation Process**:
1. Check TLAB space
2. If available, allocate in TLAB (fast path)
3. If TLAB full, get new TLAB
4. If Eden full, trigger Minor GC
</details>

**Q2.8:** What are Humongous Objects in G1GC context?



**Humongous Objects**: Objects larger than 50% of G1 heap region size
- **Allocation**: Directly allocated in Old Generation
- **Collection**: Special handling during GC cycles  
- **Impact**: Can cause performance issues if too many
- **Tuning**: Adjust `-XX:G1HeapRegionSize` to optimize
</details>

---

## 3. Stack Memory

### 3.1 Stack Basics 🟢

**Q3.1:** What is stored in Stack memory?



**Stack stores:**
- Method call frames
- Local primitive variables (int, double, boolean, etc.)
- Object references (not the objects themselves)
- Method parameters
- Return addresses
- Intermediate computation results
</details>

**Q3.2:** Is Stack memory shared between threads?



**No.** Each thread has its own Stack memory. This ensures thread safety for local variables and method calls.
</details>

**Q3.3:** What is a Stack Frame?



A **Stack Frame** is created for each method call and contains:
- **Local Variable Array**: Method parameters and local variables
- **Operand Stack**: For intermediate calculations
- **Frame Data**: Method metadata, return address, exception handling info
</details>

### 3.2 Stack Operations 🟡

**Q3.4:** How does method invocation work with Stack?



```java
public void methodA() {          // Frame A pushed
    int x = 10;                  // Local variable in Frame A
    methodB();                   // Frame B pushed on top
}                               // Frame A popped when methodA ends

public void methodB() {          // Frame B
    int y = 20;                 // Local variable in Frame B  
}                               // Frame B popped when methodB ends
```

**LIFO Order**: Last method called is first to complete.
</details>

**Q3.5:** What happens to local variables when a method exits?



**Automatic cleanup:**
- Stack frame is immediately removed
- All local variables are instantly cleared
- Object references are lost (objects become eligible for GC)
- No manual cleanup required
</details>

### 3.3 Stack Performance & Issues 🔴

**Q3.6:** What causes StackOverflowError and how to fix it?



**Causes:**
- Infinite or very deep recursion
- Too many nested method calls
- Insufficient stack size for application needs

**Solutions:**
- Fix recursive logic with base conditions
- Convert recursion to iterative approach
- Increase stack size: `-Xss2m`
- Optimize method call depth
</details>

**Q3.7:** How does Stack size affect application performance?



**Impact:**
- **Larger Stack**: More memory per thread, can handle deeper calls
- **Smaller Stack**: Less memory usage, but limited call depth
- **Trade-off**: Memory vs. functionality

**Calculation**: For 1000 threads with 1MB stack each = 1GB memory just for stacks

**Tuning**: `-Xss512k` for lightweight applications, `-Xss2m` for deep recursion
</details>

---

## 4. Method Area & Metaspace

### 4.1 Method Area Basics 🟢

**Q4.1:** What is Method Area and what does it store?



**Method Area** (implemented as Metaspace in Java 8+) stores:
- Class metadata (structure, methods, fields)
- Runtime Constant Pool
- Static variables and methods
- Method bytecode
- String literals (in Runtime Constant Pool)
</details>

**Q4.2:** What is the difference between PermGen and Metaspace?



| Aspect | PermGen (≤ Java 7) | Metaspace (Java 8+) |
|--------|-------------------|---------------------|
| **Location** | Inside Heap | Native Memory |
| **Size** | Fixed (-XX:MaxPermSize) | Dynamic growth |
| **GC** | Part of Heap GC | Separate cleanup |
| **Memory Issues** | Common OOM | Rare OOM |
| **String Pool** | In PermGen | Moved to Heap |
</details>

### 4.2 Static Variables & Methods 🟡

**Q4.3:** Where are static variables stored and when are they initialized?



**Storage**: Method Area (Metaspace)
**Initialization**: When class is first loaded by ClassLoader

```java
public class Example {
    private static int counter = 0;     // Initialized at class loading
    private static final String CONST = "Hello"; // Compile-time constant
    
    static {
        counter = 100;  // Static block executed during class initialization
    }
}
```
</details>

**Q4.4:** How do static variables behave with respect to memory and threads?



**Characteristics:**
- **Single copy**: Shared across all instances and threads
- **Memory location**: Method Area (not per-object)
- **Lifecycle**: Loaded with class, GC'd when class is unloaded
- **Thread safety**: Need synchronization if modified by multiple threads

```java
public class Counter {
    private static volatile int count = 0;  // Volatile for thread safety
    
    public static synchronized void increment() {
        count++;  // Synchronization needed for thread safety
    }
}
```
</details>

### 4.3 Class Loading & Metaspace 🔴

**Q4.5:** What causes OutOfMemoryError: Metaspace?



**Common Causes:**
- Too many classes loaded (dynamic class generation)
- ClassLoader memory leaks
- Excessive use of reflection or proxy classes
- Application servers with frequent deployments

**Solutions:**
- Increase Metaspace size: `-XX:MaxMetaspaceSize=512m`
- Fix ClassLoader leaks
- Optimize dynamic class generation
- Monitor class loading with `-XX:+TraceClassLoading`
</details>

**Q4.6:** How does class unloading work in Metaspace?



**Conditions for class unloading:**
1. No instances of the class exist
2. ClassLoader that loaded the class is eligible for GC
3. Class object itself is not referenced

**Process:**
- Metaspace cleanup happens during Full GC
- Classes are unloaded if conditions are met
- Memory is returned to the system (unlike PermGen)

**Monitoring**: `-XX:+TraceClassUnloading`
</details>

---

## 5. Garbage Collection

### 5.1 GC Fundamentals 🟢

**Q5.1:** What is Garbage Collection and why is it needed?



**Garbage Collection** is automatic memory management that:
- **Identifies** unreachable objects
- **Reclaims** memory used by these objects
- **Prevents** memory leaks
- **Optimizes** memory usage

**Need**: Java doesn't have explicit memory deallocation (no free/delete)
</details>

**Q5.2:** What makes an object eligible for Garbage Collection?



An object is eligible for GC when:
1. **No references** point to it from any reachable object
2. **Reference is set to null**: `obj = null;`
3. **Reference goes out of scope**: method ends, local variable cleared
4. **Circular references** with no external references

```java
// Eligible for GC
String str = new String("Hello");
str = null;  // Original "Hello" object eligible for GC

// Not eligible 
static List<String> list = new ArrayList<>();
list.add("Keep me");  // Referenced by static variable
```
</details>

**Q5.3:** What are the different types of Garbage Collection?



1. **Minor GC**: 
   - Scope: Young Generation only
   - Frequency: High (every few seconds)
   - Duration: Fast (milliseconds)

2. **Major GC**: 
   - Scope: Old Generation only  
   - Frequency: Low (minutes/hours)
   - Duration: Slower (tens of milliseconds to seconds)

3. **Full GC**:
   - Scope: Entire Heap + Metaspace
   - Frequency: Rare (under memory pressure)
   - Duration: Slowest (seconds)
</details>

### 5.2 GC Process & Algorithms 🟡

**Q5.4:** Explain the Mark and Sweep algorithm.



**Mark Phase:**
1. Start from GC roots (static variables, local variables, JNI references)
2. Traverse object references recursively  
3. Mark all reachable objects as "live"

**Sweep Phase:**
1. Scan through entire heap
2. Identify unmarked (dead) objects
3. Deallocate memory used by dead objects
4. Reset marks for next GC cycle

**Optional Compact Phase:**
- Move live objects together
- Eliminate fragmentation
- Update references to moved objects
</details>

**Q5.5:** What are GC roots and why are they important?



**GC Roots** are starting points for reachability analysis:

**Types of GC Roots:**
- Local variables in active method calls (Stack)
- Static variables (Method Area)
- JNI (Java Native Interface) references
- Active thread objects
- Objects in system class loader

**Importance:** Objects reachable from GC roots are considered "live" and won't be garbage collected.
</details>

**Q5.6:** How does Generational Garbage Collection work?



**Generational Hypothesis:** Most objects die young

**Strategy:**
1. **Young Generation GC**: Frequent, fast collection of short-lived objects
2. **Old Generation GC**: Infrequent, slower collection of long-lived objects
3. **Benefits**: Optimizes for common allocation patterns

**Write Barriers**: Track references from Old → Young generation for accurate GC
</details>

### 5.3 Advanced GC Concepts 🔴

**Q5.7:** What is the difference between concurrent and parallel garbage collection?



**Parallel GC:**
- Multiple GC threads work simultaneously
- All application threads are paused (Stop-the-World)
- Reduces GC time but doesn't reduce pause time
- Example: ParallelGC

**Concurrent GC:**
- GC threads run alongside application threads
- Minimal Stop-the-World pauses
- May use more CPU resources
- Example: G1GC, CMS, ZGC

**Trade-off:** Throughput vs. Latency
</details>

**Q5.8:** Explain the concept of GC ergonomics.



**GC Ergonomics**: JVM automatically tunes GC parameters based on:

**Goals:**
- **Pause Time Goal**: `-XX:MaxGCPauseMillis=200`
- **Throughput Goal**: `-XX:GCTimeRatio=99` (1% time in GC)
- **Memory Footprint**: Minimize heap size

**Auto-tuning:**
- Adjusts heap generations ratios
- Modifies GC algorithm parameters  
- Balances competing goals
- Adapts to application behavior

**Monitoring**: `-XX:+PrintGCApplicationStoppedTime`
</details>

---

## 6. GC Algorithms

### 6.1 Serial & Parallel GC 🟢

**Q6.1:** When would you use Serial GC?



**Use Cases:**
- Small applications (heap < 100MB)
- Single-core machines
- Client applications
- Embedded systems
- Development/testing environments

**Characteristics:**
- Single-threaded GC
- Simple implementation
- Low memory overhead
- Not suitable for multi-core production systems

**Flag**: `-XX:+UseSerialGC`
</details>

**Q6.2:** What is the difference between Parallel GC and Parallel Old GC?



**Parallel GC** (ParallelGC):
- Multi-threaded Minor GC (Young Generation)
- Single-threaded Major GC (Old Generation)
- Good for throughput-oriented applications

**Parallel Old GC**:
- Multi-threaded both Minor and Major GC
- Better performance for Old Generation collection
- Default before Java 9

**Flags**: 
- `-XX:+UseParallelGC`
- `-XX:+UseParallelOldGC`
</details>

### 6.2 Concurrent Mark Sweep (CMS) 🟡

**Q6.3:** How does CMS GC work and what are its phases?



**CMS Phases:**
1. **Initial Mark** (STW): Mark objects directly reachable from GC roots
2. **Concurrent Mark**: Mark all reachable objects (concurrent with app)
3. **Concurrent Preclean**: Handle objects modified during marking
4. **Remark** (STW): Finalize marking of all live objects
5. **Concurrent Sweep**: Free memory of dead objects (concurrent)
6. **Concurrent Reset**: Prepare for next cycle

**Benefits**: Low pause times
**Drawbacks**: CPU overhead, fragmentation, deprecated in Java 9+
</details>

**Q6.4:** What problems can occur with CMS GC?



**Issues:**
1. **Fragmentation**: No compaction phase leads to memory fragmentation
2. **Concurrent Mode Failure**: CMS can't keep up with allocation rate
3. **CPU Overhead**: Background threads consume CPU
4. **Promotion Failure**: Young Gen objects can't be promoted due to fragmentation

**Solutions:**
- Increase heap size
- Tune CMS trigger points: `-XX:CMSInitiatingOccupancyFraction=70`
- Switch to G1GC (modern alternative)
</details>

### 6.3 G1 Garbage Collector 🟡

**Q6.5:** How does G1GC differ from traditional generational collectors?



**Traditional GC**: Fixed Young/Old generation regions
**G1GC**: Heap divided into equal-sized regions (1MB-32MB)

**G1 Regions:**
- **Eden Regions**: New object allocation
- **Survivor Regions**: Objects that survived GC
- **Old Regions**: Long-lived objects
- **Humongous Regions**: Large objects (>50% region size)

**Benefits:**
- Predictable pause times
- Better handling of large heaps
- Incremental collection
- Automatic tuning
</details>

**Q6.6:** What is the G1GC evacuation process?



**Evacuation**: Moving live objects from selected regions

**Process:**
1. **Pause Prediction**: Select regions based on garbage ratio and pause goal
2. **Root Scanning**: Find references to selected regions
3. **Update Remembered Sets**: Track inter-region references
4. **Object Copy**: Evacuate live objects to new regions
5. **Reference Update**: Update all references to moved objects

**Goal**: Achieve target pause time (`-XX:MaxGCPauseMillis=200`)
</details>

### 6.4 Low-Latency Collectors 🔴

**Q6.7:** What are the key features of ZGC?



**ZGC Features:**
- **Ultra-low pause times**: <10ms regardless of heap size
- **Concurrent operations**: Most work done concurrently
- **Colored pointers**: Encode object state in pointer
- **Load barriers**: Check object state during access
- **Massive heap support**: Up to 16TB

**Trade-offs:**
- Higher memory overhead (2-16%)
- CPU overhead for load barriers
- Requires Java 11+

**Flag**: `-XX:+UseZGC`
</details>

**Q6.8:** When should you consider using Shenandoah GC?



**Use Cases:**
- Applications requiring consistent low latency
- Large heaps (multi-GB) with latency requirements
- Real-time or near-real-time systems
- When pause time is more critical than throughput

**Characteristics:**
- Concurrent evacuation
- Low pause times (typically <10ms)
- Available in OpenJDK
- Similar goals to ZGC with different implementation

**Flag**: `-XX:+UseShenandoahGC`
</details>

---

## 7. Memory Tuning & Performance

### 7.1 JVM Parameters 🟢

**Q7.1:** What are the most important JVM memory tuning parameters?



**Heap Sizing:**
- `-Xms2g`: Initial heap size
- `-Xmx8g`: Maximum heap size
- `-XX:NewRatio=3`: Old:Young generation ratio

**Stack Sizing:**
- `-Xss1m`: Stack size per thread

**Metaspace:**
- `-XX:MetaspaceSize=256m`: Initial metaspace size
- `-XX:MaxMetaspaceSize=512m`: Maximum metaspace size

**GC Selection:**
- `-XX:+UseG1GC`: Use G1 garbage collector
- `-XX:MaxGCPauseMillis=200`: Target pause time
</details>

**Q7.2:** How do you calculate appropriate heap size for an application?



**Calculation factors:**
1. **Available system memory**: Leave memory for OS and other processes
2. **Application requirements**: Expected object allocation rates
3. **GC overhead**: More heap = less frequent GC but longer pauses
4. **Rule of thumb**: Start with 25-50% of available RAM

**Example:**
- System: 16GB RAM
- OS + other processes: 4GB
- Available for JVM: 12GB
- Recommended heap: 6-8GB
- Command: `-Xms6g -Xmx8g`
</details>

### 7.2 Performance Monitoring 🟡

**Q7.3:** What tools can you use to monitor Java memory usage?



**Built-in Tools:**
- **JConsole**: GUI monitoring tool
- **JVisualVM**: Visual profiler with heap dumps
- **Java Flight Recorder (JFR)**: Low-overhead profiling
- **jstat**: Command-line statistics

**Third-party Tools:**
- **Eclipse MAT**: Memory Analyzer Tool for heap dumps
- **GCViewer**: GC log analysis
- **JProfiler**: Commercial profiler
- **AppDynamics/New Relic**: APM tools

**Command Examples:**
```bash
jstat -gc -t <pid> 250ms    # GC statistics every 250ms
jmap -histo <pid>           # Histogram of objects
jmap -dump:file=heap.hprof <pid>  # Generate heap dump
```
</details>

**Q7.4:** How do you analyze GC logs for performance issues?



**Key Metrics:**
1. **GC Frequency**: How often GC occurs
2. **GC Duration**: Time spent in each GC cycle
3. **Throughput**: % time application vs. GC
4. **Memory Usage**: Heap utilization patterns

**Red Flags:**
- Frequent Full GC events
- Long GC pause times (>100ms for low-latency apps)
- Increasing Old Generation usage
- OutOfMemoryErrors

**Analysis Steps:**
1. Enable GC logging: `-XX:+PrintGCDetails -XX:+PrintGCTimeStamps`
2. Use tools like GCViewer or GCPlot.com
3. Identify bottlenecks and tune accordingly
</details>

### 7.3 Memory Optimization 🔴

**Q7.5:** What are the best practices for minimizing GC pressure?



**Object Allocation Optimization:**
```java
// ❌ BAD - Creates many temporary objects
public String buildString(List<String> items) {
    String result = "";
    for (String item : items) {
        result += item;  // New String object each iteration
    }
    return result;
}

// ✅ GOOD - Reuses StringBuilder
public String buildString(List<String> items) {
    StringBuilder sb = new StringBuilder(items.size() * 16);
    for (String item : items) {
        sb.append(item);
    }
    return sb.toString();
}
```

**Other Practices:**
- Object pooling for expensive objects
- Primitive collections (avoid boxing)
- Lazy initialization
- Proper collection sizing
- Avoid memory leaks (listeners, static references)
</details>

**Q7.6:** How do you handle memory leaks in Java applications?



**Common Leak Patterns:**
1. **Static Collections**: Unbounded growth
2. **Listeners**: Not removing event listeners
3. **ThreadLocal**: Not calling `remove()`
4. **Inner Classes**: Holding outer class references
5. **ClassLoader Leaks**: In application servers

**Detection:**
- Heap dump analysis with Eclipse MAT
- Monitor heap growth over time
- Profile with tools like JProfiler

**Prevention:**
```java
// ✅ Proper cleanup
public class LeakPrevention {
    private static final Map<String, Object> cache = 
        new ConcurrentHashMap<>();
    
    // Bounded cache
    public void addToCache(String key, Object value) {
        if (cache.size() > 10000) {
            cache.clear(); // Simple strategy
        }
        cache.put(key, value);
    }
    
    // ThreadLocal cleanup
    private static ThreadLocal<Object> threadLocal = new ThreadLocal<>();
    
    public void cleanup() {
        try {
            // Use ThreadLocal
        } finally {
            threadLocal.remove(); // Always clean up
        }
    }
}
```
</details>

---

## 8. Troubleshooting & Errors

### 8.1 OutOfMemoryError Variants 🟢

**Q8.1:** What are the different types of OutOfMemoryError?



1. **`OutOfMemoryError: Java heap space`**
   - Cause: Heap memory exhausted
   - Solution: Increase `-Xmx`, fix memory leaks

2. **`OutOfMemoryError: Metaspace`**  
   - Cause: Too many classes loaded
   - Solution: Increase `-XX:MaxMetaspaceSize`

3. **`OutOfMemoryError: GC overhead limit exceeded`**
   - Cause: >98% time in GC, <2% memory freed
   - Solution: Increase heap, optimize code

4. **`OutOfMemoryError: Direct buffer memory`**
   - Cause: NIO direct buffers exhausted
   - Solution: Increase `-XX:MaxDirectMemorySize`
</details>

**Q8.2:** What causes StackOverflowError and how to debug it?



**Common Causes:**
- Infinite recursion
- Very deep method call chains
- Large local variables
- Small stack size

**Debugging Steps:**
1. Check stack trace for recursive patterns
2. Increase stack size temporarily: `-Xss2m`
3. Profile call depth
4. Fix recursive logic or convert to iterative

**Example:**
```java
// ❌ Infinite recursion
public int factorial(int n) {
    return n * factorial(n - 1); // Missing base case
}

// ✅ Fixed with base case
public int factorial(int n) {
    if (n