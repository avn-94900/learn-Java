
## Virtual Threads

### What are Virtual Threads?

Virtual threads are lightweight threads managed by the JVM, not the operating system. They enable writing scalable concurrent applications by reducing the overhead of thread creation and management.

### Virtual Threads vs Platform Threads - Architecture & Lifecycle

| Aspect | Platform Threads | Virtual Threads |
|--------|-----------------|-----------------|
| **Architecture** | Heavy OS-level threads, 1:1 mapping with OS kernel threads | Lightweight Java-level threads, many-to-one mapping to carrier threads |
| **Memory Consumption** | Each thread consumes significant memory (~1-2 MB stack allocation) | Minimal memory footprint (~few KB per thread) |
| **Management** | Managed directly by the operating system scheduler | Managed by Project Loom's scheduler, not the OS |
| **Execution** | Run directly on OS threads | Run on top of platform threads (carrier threads) from a ForkJoinPool |

### Working Mechanism

| Aspect | Platform Threads | Virtual Threads |
|--------|-----------------|-----------------|
| **Blocking Operations** | Cause OS context switching, expensive | Non-blocking at OS level; unmounted from carrier thread when blocking occurs |
| **Thread Pool Limitations** | Typically limited to number of CPU cores | Can create millions of virtual threads efficiently |
| **Concurrency** | Limited concurrency due to OS thread management | High concurrency with minimal overhead |

### Lifecycle Differences

| Aspect | Platform Threads | Virtual Threads |
|--------|-----------------|-----------------|
| **Lifecycle States** | NEW -> RUNNABLE -> RUNNING -> (blocked/waiting) -> RUNNABLE -> TERMINATED | NEW -> RUNNABLE -> RUNNING -> (mounted on carrier) -> (unmounted if blocked) -> RUNNABLE -> RUNNING -> TERMINATED |
| **Creation Cost** | Expensive (involves OS kernel calls) | Cheap (pure Java objects) |
| **Context Switching** | Heavy, involves CPU state save/restore | Fast transitions to carrier threads |
| **Memory Management** | Static allocation throughout lifetime | Minimal, reused across lifecycle |
| **Blocking Impact** | Entire OS thread blocked | Only virtual thread paused, carrier thread continues |

---

### Creating Virtual Threads

#### Method 1: Using Thread.ofVirtual()
```java
Thread vThread = Thread.ofVirtual().start(() -> {
    System.out.println("Running in virtual thread");
});
```

#### Method 2: Using ExecutorService
```java
try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
    executor.submit(() -> {
        System.out.println("Task running in virtual thread");
    });
}
```

#### Method 3: Using Thread.startVirtualThread()
```java
Thread.startVirtualThread(() -> {
    System.out.println("Direct virtual thread creation");
});
```

---

### Use Cases for Virtual Threads

#### ✅ Ideal Scenarios
- I/O-intensive applications (file, network operations,handle http req)
- High-concurrency workloads (web servers, APIs)
- Microservices handling thousands of concurrent requests
- Applications with many blocking operations
- Replacing thread pools for task-per-request models

#### ❌ Avoid For
- CPU-intensive tasks (no benefit, scheduling overhead)
- Real-time applications requiring strict latency guarantees
- Applications already using async/reactive programming models

---

### Carrier Threads Concept

**Carrier threads** are the actual OS threads that execute virtual threads. The JVM manages virtual thread mounting and unmounting:

```
Virtual Thread Lifecycle:
┌─────────────────────────────────────────────┐
│ Virtual Thread (Task)                       │
├─────────────────────────────────────────────┤
│ MOUNTED → Executes on Carrier Thread        │
│           ↓ (Blocking I/O)                  │
│ UNMOUNTED → Carrier Thread freed for others │
│           ↓ (I/O completes)                 │
│ MOUNTED → Runs on available Carrier Thread  │
└─────────────────────────────────────────────┘
```

---

### Common Pitfalls

| Pitfall | Impact | Mitigation |
|---------|--------|-----------|
| **Thread-local Variables** | Memory leaks if not cleaned | Use scoped values or clean up properly |
| **Synchronized Blocks** | Pins virtual thread to carrier | Use `ReentrantLock` or non-blocking code |
| **JNI Calls** | Pins virtual thread to carrier | Minimize native calls or use alternatives |
| **CPU-bound Tasks** | Scheduling overhead, poor performance | Use platform threads instead |

---

### Thread-Local Variables Example

```java
// ❌ Problematic: Can cause memory leaks
ThreadLocal<Connection> connThreadLocal = new ThreadLocal<>();

public record VirtualThreadExample() {
    public void processRequest() {
        connThreadLocal.set(getConnection());
        // If not removed, connection leaks across many virtual threads
        // connThreadLocal.remove(); // Must explicitly clean up
    }
}

// ✅ Better: Use scoped values (Java 21+)
static final ScopedValue<Connection> CONNECTION = ScopedValue.newInstance();

public void processRequestScoped() {
    ScopedValue.where(CONNECTION, getConnection()).run(this::execute);
}
```

---

### Synchronized Blocks and Virtual Threads

```java
// ❌ Problematic: Pins virtual thread to carrier thread
public synchronized void criticalSection() {
    // Virtual thread is pinned here, reducing concurrency benefits
}

// ✅ Better: Use ReentrantLock
private final ReentrantLock lock = new ReentrantLock();

public void criticalSection() {
    lock.lock();
    try {
        // Non-blocking, virtual thread can be unmounted
    } finally {
        lock.unlock();
    }
}
```

---

### Performance Comparison Example

```java
// Platform Threads: Limited by OS capacity
try (var executor = Executors.newFixedThreadPool(100)) {
    for (int i = 0; i < 100_000; i++) {
        executor.submit(this::blockingIoTask);
        // Queue overflows or threads exhaust system resources
    }
}

// Virtual Threads: Handle millions efficiently
try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
    for (int i = 0; i < 1_000_000; i++) {
        executor.submit(this::blockingIoTask);
        // Handles seamlessly with minimal overhead
    }
}
```
