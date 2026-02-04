
# Java Memory Management - Complete Guide

## 📋 Table of Contents
1. [Memory Architecture Overview](#memory-architecture-overview)
2. [Heap Memory (Object Storage)](#heap-memory-object-storage)
3. [Stack Memory (Method Execution)](#stack-memory-method-execution)
4. [Method Area & Metaspace](#method-area--metaspace)
5. [Garbage Collection](#garbage-collection)
6. [Memory Pools](#memory-pools)
7. [Practical Examples](#practical-examples)
8. [Interview Questions & Tips](#interview-questions--tips)

---

## 🏗️ Memory Architecture Overview

Java memory is divided into **two main categories**:

```
JVM Memory
├── Heap Memory (Objects)
│   ├── Young Generation
│   │   ├── Eden Space
│   │   ├── Survivor 0 (S0)
│   │   └── Survivor 1 (S1)
│   └── Old Generation (Tenured)
│
└── Non-Heap Memory
    ├── Stack Memory (per thread)
    ├── Method Area/Metaspace
    ├── PC Registers
    └── Native Method Stacks
```

### Key Principle
- **Heap**: Stores all objects and instance variables
- **Stack**: Stores method calls, local variables, and object references
- **Method Area**: Stores class-level information

---

## 🗂️ Heap Memory (Object Storage)

### Structure & Flow

```
New Object Creation Flow:
[New Object] → Eden Space → Survivor 0/1 → Old Generation
```

### 1. Young Generation (New Objects)

#### **Eden Space**
- **Purpose**: All new objects are created here
- **Size**: Largest portion of Young Generation
- **Lifecycle**: Objects stay until first GC cycle

#### **Survivor Spaces (S0 & S1)**
- **Purpose**: Hold objects that survived at least one GC
- **Mechanism**: Only one survivor space is active at a time
- **Rule**: Objects move between S0 ↔ S1 during minor GC

### 2. Old Generation (Tenured)
- **Purpose**: Long-lived objects that survived multiple GC cycles
- **Promotion**: Objects move here after surviving 8-15 GC cycles (configurable)
- **Size**: Larger than Young Generation

### Memory Allocation Example
```java
// These objects start in Eden Space
String name = new String("John");           // Object in Eden
List<String> list = new ArrayList<>();     // Object in Eden
Person person = new Person("Alice", 25);   // Object in Eden

// After multiple GC cycles, long-lived objects move to Old Generation
```

---

## Stack Memory Explained

### Characteristics

| **Aspect** | **Details** |
|------------|-------------|
| **Scope** | Thread-specific (each thread has its own) |
| **Structure** | LIFO (Last In, First Out) |
| **Contents** | Method calls, local variables, references |
| **Management** | Automatic (no manual cleanup needed) |
| **Speed** | Very fast allocation/deallocation |

### Stack Frame Components

Each method call creates a **Stack Frame** containing:
1. **Local Variables**: Primitive values and object references
2. **Operand Stack**: Intermediate calculations
3. **Return Address**: Where to continue after method ends

### Stack vs Heap Example

```java
public class StackHeapExample {
    private static int staticVar = 100;    // Metaspace
    private int instanceVar = 50;          // Heap (with object)
    
    public void demonstrateMemory() {      // Method in Metaspace
        // === STACK MEMORY ===
        int localPrimitive = 42;           // Stored directly in Stack
        String localReference = "Hello";   // Reference in Stack
        List<String> localList = new ArrayList<>(); // Reference in Stack
        
        // === HEAP MEMORY ===
        // "Hello" string object → String Pool (Heap)
        // ArrayList object → Heap
        // this.instanceVar → Heap (part of object instance)
        
        processData(localPrimitive);       // New stack frame created
    } // Stack frame destroyed here, local variables cleared
    
    private void processData(int param) {  // New stack frame
        int result = param * 2;            // Local to this stack frame
    } // Stack frame destroyed, 'param' and 'result' cleared
}
```
### Stack Memory Visualization

```
Stack Memory (Thread 1):
┌─────────────────────────┐ ← Top of Stack
│  processData()          │
│  - param: 42            │
│  - result: 84           │
├─────────────────────────┤
│  demonstrateMemory()    │
│  - localPrimitive: 42   │
│  - localReference: ref→ │ ──┐
│  - localList: ref→      │ ──┼─→ Heap Objects
├─────────────────────────┤   │
│  main()                 │   │
│  - args: ref→           │ ──┘
└─────────────────────────┘
```

---

## 🏛️ Method Area & Metaspace

### Java 8+ (Metaspace)
Replaced PermGen with **Metaspace** for better memory management:

#### **What's Stored**:
- **Class Metadata**: Class structure, method signatures
- **Runtime Constant Pool**: String literals, numeric constants
- **Static Variables**: Class-level variables
- **Method Bytecode**: Compiled method instructions

#### **Key Improvements over PermGen**:
- **Dynamic Sizing**: Grows automatically
- **Native Memory**: Uses system memory, not heap
- **Better GC**: Reduced OutOfMemoryError issues

### Static vs Instance Memory
```java
public class MemoryExample {
    // Stored in Method Area
    private static int staticVar = 100;
    private static final String CONSTANT = "Hello";
    
    // Stored in Heap (with object instance)
    private int instanceVar = 50;
    
    public static void staticMethod() {
        // Method definition stored in Method Area
        int localVar = 10;  // Stored in Stack when method executes
    }
}
```

---

## Garbage Collection

### GC Types & Triggers

| **GC Type** | **Scope** | **Trigger** | **Performance** |
|-------------|-----------|-------------|-----------------|
| **Minor GC** | Young Generation | Eden space full | Fast (milliseconds) |
| **Major GC** | Old Generation | Old Gen full | Slow (can cause pauses) |
| **Full GC** | Entire Heap + Metaspace | Memory pressure | Slowest (significant pause) |

### GC Process Visualization

```
BEFORE Minor GC:
┌──────────────────────────────────────────────────────┐
│ Young Generation                                     │
│ Eden: [Obj1][Obj2][Obj3][Obj4] ← FULL                │
│ S0:   [OldObj1][OldObj2]                             │
│ S1:   [empty]                                        │
└──────────────────────────────────────────────────────┘

AFTER Minor GC:
┌──────────────────────────────────────────────────────┐
│ Young Generation                                     │
│ Eden: [cleared]                                      │
│ S0:   [cleared]                                      │
│ S1:   [OldObj1][OldObj2][Obj2][Obj4] ← Active        │
│       (Obj1, Obj3 were garbage collected)            │
└──────────────────────────────────────────────────────┘
```

### Reference Types (GC Behavior)

```java
// === STRONG REFERENCE (Default) ===
Object obj = new Object();              // Never GC'd until reference nulled

// === SOFT REFERENCE (Memory-sensitive) ===
SoftReference<Object> soft = new SoftReference<>(new Object());
// GC'd only under memory pressure (good for caches)

// === WEAK REFERENCE (GC-friendly) ===
WeakReference<Object> weak = new WeakReference<>(new Object());
// GC'd in next collection cycle (used in WeakHashMap)

// === PHANTOM REFERENCE (Post-finalization) ===
ReferenceQueue<Object> queue = new ReferenceQueue<>();
PhantomReference<Object> phantom = new PhantomReference<>(new Object(), queue);
// Used for cleanup tasks after object finalization
```

---

## GC Algorithms Comparison

### Algorithm Overview

| **Collector** | **Threads** | **Pause Time** | **Throughput** | **Best For** |
|---------------|-------------|----------------|----------------|--------------|
| **Serial GC** | Single | High | Low | Small apps, embedded systems |
| **Parallel GC** | Multiple | Medium | High | Batch processing, throughput-focused |
| **CMS** | Concurrent | Low | Medium | Low-latency apps (deprecated Java 9+) |
| **G1 GC** | Concurrent | Low (tunable) | High | Modern default, balanced performance |
| **ZGC/Shenandoah** | Concurrent | Ultra-low (<10ms) | High | Massive heaps, ultra-low latency |

### When to Use Each GC

```java
// JVM Flags for GC Selection:

// Small applications, single-core
-XX:+UseSerialGC

// High throughput, batch processing
-XX:+UseParallelGC

// Balanced performance (default Java 9+)
-XX:+UseG1GC
-XX:MaxGCPauseMillis=200    // Target pause time

// Ultra-low latency, large heaps
-XX:+UseZGC                 // Java 11+
-XX:+UseShenandoahGC        // OpenJDK
```

### Choosing the Right GC

**Decision Tree:**
1. **Small heap (<100MB)** → Serial GC
2. **Throughput > Latency** → Parallel GC
3. **Balanced needs** → G1 GC (default choice)
4. **Ultra-low latency** → ZGC/Shenandoah
5. **Legacy low-latency** → CMS (avoid in new projects)

---

## Memory Tuning & Best Practices

### JVM Memory Parameters

```bash
# === HEAP SIZING ===
-Xms2g                     # Initial heap size
-Xmx8g                     # Maximum heap size
-XX:NewRatio=3             # Old:Young ratio (3:1)

# === STACK SIZING ===
-Xss1m                     # Stack size per thread

# === METASPACE ===
-XX:MetaspaceSize=256m     # Initial metaspace size
-XX:MaxMetaspaceSize=512m  # Maximum metaspace size

# === GC TUNING ===
-XX:+UseG1GC               # Use G1 collector
-XX:MaxGCPauseMillis=100   # Target max pause time
-XX:G1HeapRegionSize=16m   # G1 region size

# === MONITORING ===
-XX:+PrintGC               # Basic GC logging
-XX:+PrintGCDetails        # Detailed GC information
-XX:+PrintGCTimeStamps     # Add timestamps
-XX:+HeapDumpOnOutOfMemoryError  # Generate dump on OOM
```

### Memory Optimization Techniques

#### **Avoid Memory Leaks**
```java
// ❌ BAD - Memory leak with static collection
public class BadExample {
    private static List<String> cache = new ArrayList<>();
    
    public void addData(String data) {
        cache.add(data);  // Grows indefinitely!
    }
}

// ✅ GOOD - Bounded cache with cleanup
public class GoodExample {
    private static final int MAX_SIZE = 1000;
    private static final Map<String, String> cache = new LinkedHashMap<String, String>(16, 0.75f, true) {
        protected boolean removeEldestEntry(Map.Entry<String, String> eldest) {
            return size() > MAX_SIZE;
        }
    };
}
```

#### **Optimize Object Creation**
```java
// ❌ BAD - Creates many temporary objects
public String concatenateStrings(List<String> strings) {
    String result = "";
    for (String s : strings) {
        result += s;  // New String object each iteration
    }
    return result;
}

// ✅ GOOD - Reuses StringBuilder buffer
public String concatenateStrings(List<String> strings) {
    StringBuilder sb = new StringBuilder(strings.size() * 16); // Pre-size
    for (String s : strings) {
        sb.append(s);
    }
    return sb.toString();
}

// ✅ BETTER - Use Java 8+ String.join()
public String concatenateStrings(List<String> strings) {
    return String.join("", strings);
}
```

#### **Resource Management**
```java
// ✅ Proper resource cleanup
try (FileInputStream fis = new FileInputStream("file.txt");
     BufferedReader reader = new BufferedReader(new InputStreamReader(fis))) {
    
    return reader.lines().collect(Collectors.toList());
} // Resources automatically closed
```

### Memory Monitoring

```java
// Runtime memory information
Runtime runtime = Runtime.getRuntime();
long maxMemory = runtime.maxMemory();     // -Xmx value
long totalMemory = runtime.totalMemory(); // Currently allocated
long freeMemory = runtime.freeMemory();   // Available in allocated

// Memory usage calculation
long usedMemory = totalMemory - freeMemory;
System.out.printf("Used: %d MB, Free: %d MB, Max: %d MB%n",
    usedMemory / 1024 / 1024,
    freeMemory / 1024 / 1024,
    maxMemory / 1024 / 1024);

// Suggest garbage collection (not guaranteed)
System.gc();
```

---

## Common Errors & Troubleshooting

### OutOfMemoryError Variants

| **Error Type** | **Cause** | **Solution** |
|----------------|-----------|--------------|
| `OutOfMemoryError: Java heap space` | Heap exhausted | Increase `-Xmx`, fix memory leaks |
| `OutOfMemoryError: Metaspace` | Too many classes loaded | Increase `-XX:MaxMetaspaceSize` |
| `OutOfMemoryError: GC overhead limit` | 98% time in GC, <2% memory freed | Increase heap, optimize code |
| `StackOverflowError` | Stack exhausted (deep recursion) | Increase `-Xss`, fix recursion |

### Troubleshooting Steps

1. **Enable GC Logging**
   ```bash
   -XX:+PrintGC -XX:+PrintGCDetails -XX:+PrintGCTimeStamps
   ```

2. **Generate Heap Dump**
   ```bash
   -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/path/to/dumps/
   ```

3. **Analyze with Tools**
   - **Eclipse MAT**: Heap dump analysis
   - **JVisualVM**: Real-time monitoring
   - **JConsole**: JMX-based monitoring
   - **GCViewer**: GC log analysis

### Memory Leak Detection

```java
// Common leak patterns to avoid:

// ❌ Static collections that grow indefinitely
private static List<Object> staticList = new ArrayList<>();

// ❌ Listeners not removed
button.addActionListener(listener);  // Remove in cleanup!

// ❌ ThreadLocal not cleaned
private static ThreadLocal<Object> threadLocal = new ThreadLocal<>();
// Use threadLocal.remove() in finally block

// ❌ Inner class holding outer reference
public class OuterClass {
    class InnerClass {
        // Implicitly holds reference to OuterClass
        // Can prevent GC of outer instance
    }
}
```

---

## Interview Questions & Code Examples

### Essential Interview Questions

#### **Q1: Explain the difference between Heap and Stack memory**

```java
public class MemoryDemo {
    private static int staticVar = 100;        // Metaspace
    private int instanceVar = 200;             // Heap (with object)
    
    public void methodDemo() {
        int localVar = 300;                    // Stack
        String localString = "Hello";          // Reference: Stack, Object: String Pool
        Object localObject = new Object();     // Reference: Stack, Object: Heap
        
        // All local variables cleared when method ends
    }
}

// Answer:
// - Stack: Thread-specific, stores method calls and local variables
// - Heap: Shared, stores all objects and instance variables
// - References stored in Stack point to objects in Heap
```

#### **Q2: What happens during Garbage Collection?**

```java
public class GCDemo {
    public static void main(String[] args) {
        // Objects created in Eden space
        List<String> list1 = new ArrayList<>();  // Survives (referenced)
        List<String> list2 = new ArrayList<>();  // May be GC'd (unreferenced later)
        
        list1.add("Keep me");
        list2.add("Temporary");
        
        list2 = null;  // Now eligible for GC
        
        // Minor GC triggered when Eden fills:
        // 1. Mark reachable objects (list1)
        // 2. Copy survivors to S0/S1
        // 3. Clear Eden and inactive Survivor space
        // 4. Objects surviving multiple cycles → Old Generation
    }
}

// Answer:
// 1. Mark phase: Identify reachable objects
// 2. Sweep phase: Remove unreferenced objects
// 3. Compact phase: Defragment memory (in some GCs)
// 4. Promotion: Move long-lived objects to Old Generation
```

#### **Q3: When would you get StackOverflowError vs OutOfMemoryError?**

```java
// StackOverflowError example
public class StackOverflowDemo {
    public void recursiveMethod() {
        recursiveMethod();  // Infinite recursion
    }
    // Causes: Deep method calls exhaust stack space
}

// OutOfMemoryError example  
public class OOMDemo {
    public static void main(String[] args) {
        List<byte[]> memoryHog = new ArrayList<>();
        while (true) {
            memoryHog.add(new byte[1024 * 1024]);  // 1MB each
        }
        // Causes: Heap space exhausted by too many objects
    }
}

// Answer:
// - StackOverflowError: Stack memory exhausted (usually recursion)
// - OutOfMemoryError: Heap memory exhausted (too many objects)
```

### Advanced Scenarios

#### **String Pool Behavior**
```java
public class StringPoolDemo {
    public static void main(String[] args) {
        String s1 = "Hello";                    // String pool
        String s2 = "Hello";                    // Reuses from pool
        String s3 = new String("Hello");        // New heap object
        String s4 = s3.intern();                // Returns pool reference
        
        System.out.println(s1 == s2);           // true (same pool object)
        System.out.println(s1 == s3);           // false (different objects)
        System.out.println(s1 == s4);           // true (both from pool)
        System.out.println(s1.equals(s3));      // true (same content)
    }
}
```

#### **Memory-Efficient Collections**
```java
public class MemoryEfficientCollections {
    // ❌ Memory-heavy approach
    public Map<String, String> createHeavyMap() {
        Map<String, String> map = new HashMap<>();
        // HashMap has overhead: ~32 bytes per entry + key/value objects
        return map;
    }
    
    // ✅ Memory-efficient alternatives
    public Map<String, String> createEfficientMap() {
        // Pre-size to avoid rehashing
        return new HashMap<>(16, 0.75f);
    }
    
    // ✅ Use specialized collections for primitives
    // TIntObjectHashMap from Trove library (avoids boxing)
    // Or Eclipse Collections for better memory usage
}
```

### Performance Analysis

```java
public class PerformanceAnalysis {
    // Measure memory allocation
    public void measureMemoryUsage() {
        Runtime runtime = Runtime.getRuntime();
        
        long beforeMemory = runtime.totalMemory() - runtime.freeMemory();
        
        // Your code here
        List<String> data = createLargeDataSet();
        
        long afterMemory = runtime.totalMemory() - runtime.freeMemory();
        
        System.out.println("Memory used: " + (afterMemory - beforeMemory) + " bytes");
    }
    
    // GC-friendly code patterns
    public void efficientProcessing() {
        // Reuse objects when possible
        StringBuilder sb = new StringBuilder(1024);  // Pre-size
        
        // Process in batches to avoid overwhelming young generation
        int batchSize = 1000;
        for (int i = 0; i < data.size(); i += batchSize) {
            processBatch(data.subList(i, Math.min(i + batchSize, data.size())));
            
            // Suggest GC between batches for large datasets
            if (i % 10000 == 0) {
                System.gc();  // Hint only, not guaranteed
            }
        }
    }
}
```

---

## 🚀 Summary & Key Takeaways

### Memory Areas Quick Reference
- **Heap**: All objects, arrays, instance variables (shared)
- **Stack**: Method calls, local variables, references (per-thread)
- **Metaspace**: Class metadata, static variables, method bytecode (shared)

### GC Best Practices
1. **Choose appropriate GC**: G1 for most modern applications
2. **Size heap correctly**: Start with `-Xms` = `-Xmx` for production
3. **Monitor GC logs**: Identify performance bottlenecks
4. **Avoid memory leaks**: Clean up resources, remove listeners

### Performance Tips
1. **Minimize object creation** in hot code paths
2. **Use StringBuilder** for string concatenation
3. **Pre-size collections** when size is known
4. **Profile memory usage** with tools like JProfiler or VisualVM

### Interview Success
- **Understand the big picture**: How Heap, Stack, and Metaspace work together
- **Know common errors**: OOM causes and solutions
- **Practice code analysis**: Identify where variables/objects are stored
- **Be familiar with GC**: Basic understanding of minor/major GC cycles

Remember: Java memory management is largely automatic, but understanding it helps you write more efficient code and troubleshoot performance issues effectively.