


# Java Virtual Threads

## What are Virtual Threads?

Virtual threads are lightweight threads managed by the JVM, not the operating system. Introduced in Java 21 (Project Loom), they let you write blocking-style code that scales like async code — without the complexity of reactive programming.

---

## The Problem They Solve

With platform threads, each thread maps 1:1 to an OS thread. OS threads are expensive (~1–2 MB of stack memory each), so thread pools cap concurrency. Under high I/O load, threads spend most of their time blocked and wasted.

Virtual threads solve this by unmounting from the carrier thread during blocking operations, freeing it to run other virtual threads.

---

## Platform Threads vs Virtual Threads

| Aspect | Platform Thread | Virtual Thread |
|--------|----------------|----------------|
| Managed by | Operating system | JVM (Project Loom) |
| Memory per thread | ~1–2 MB | ~few KB |
| Creation cost | Expensive (OS kernel call) | Cheap (pure Java object) |
| Context switching | Heavy (CPU state save/restore) | Fast (JVM-level) |
| Blocking impact | Entire OS thread blocked | Only virtual thread paused; carrier thread freed |
| Practical limit | Hundreds to low thousands | Millions |
| Runs on | OS thread directly | Carrier thread (ForkJoinPool) |

---

## How Virtual Threads Work

A **carrier thread** is a real OS thread that a virtual thread mounts onto to execute. When the virtual thread hits a blocking call, it unmounts and the carrier thread picks up another virtual thread.

```
Virtual thread hits blocking I/O
        |
        v
Unmounted from carrier thread
Carrier thread runs other virtual threads
        |
        v
I/O completes — virtual thread remounted on any available carrier thread
        |
        v
Continues execution
```

This means thousands of virtual threads can share a small pool of carrier threads with almost no wasted capacity.

---

## Creating Virtual Threads

### Method 1: Thread.ofVirtual()

```java
Thread vt = Thread.ofVirtual().start(() -> {
    System.out.println("Running in virtual thread: " + Thread.currentThread());
});
vt.join();
```

### Method 2: ExecutorService (recommended for task-per-request)

```java
try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
    for (int i = 0; i < 1_000_000; i++) {
        executor.submit(this::handleRequest);
    }
} // auto-closes and waits for all tasks
```

### Method 3: Thread.startVirtualThread()

```java
Thread.startVirtualThread(() -> System.out.println("Quick fire-and-forget"));
```

---

## When to Use (and When Not To)

**Good fit — I/O-bound workloads:**
- HTTP servers handling many concurrent requests
- Database query execution
- File I/O and network calls
- Microservices with task-per-request models

**Poor fit — CPU-bound workloads:**
- Heavy computation (no blocking, so no benefit from unmounting)
- Real-time systems with strict latency guarantees
- Code already using reactive/async pipelines — introducing virtual threads adds complexity without gain

---

## Common Pitfalls

### Synchronized blocks pin the virtual thread

`synchronized` prevents unmounting, locking the carrier thread for the duration of the block and eliminating the concurrency benefit.

```java
// Problematic — virtual thread is pinned to carrier
public synchronized void criticalSection() {
    doWork();
}

// Correct — ReentrantLock allows unmounting
private final ReentrantLock lock = new ReentrantLock();

public void criticalSection() {
    lock.lock();
    try {
        doWork();
    } finally {
        lock.unlock();
    }
}
```

### ThreadLocal can cause memory leaks

With millions of short-lived virtual threads, `ThreadLocal` values that are never removed accumulate. Prefer `ScopedValue` (Java 21+), which is automatically cleaned up.

```java
// Problematic — remove() is easy to miss
static final ThreadLocal<Connection> CONN = new ThreadLocal<>();

void process() {
    CONN.set(getConnection());
    doWork();
    CONN.remove(); // forgetting this leaks the connection
}

// Correct — scoped values are cleaned up automatically
static final ScopedValue<Connection> CONN = ScopedValue.newInstance();

void process() {
    ScopedValue.where(CONN, getConnection()).run(this::doWork);
}
```

### JNI calls also pin the carrier thread

Native (JNI) calls prevent unmounting just like `synchronized`. Minimize native calls on the hot path, or isolate them to platform threads.

---

## Platform Threads vs Virtual Threads: Side by Side

```java
// Platform threads — capacity limited by OS
try (var executor = Executors.newFixedThreadPool(200)) {
    for (int i = 0; i < 100_000; i++) {
        executor.submit(this::blockingIoTask);
        // Queue backs up; threads are scarce
    }
}

// Virtual threads — scales to millions
try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
    for (int i = 0; i < 1_000_000; i++) {
        executor.submit(this::blockingIoTask);
        // Handles efficiently; carrier threads never sit idle
    }
}
```

---

## Pitfall Summary

| Pitfall | Why It Hurts | Fix |
|---------|-------------|-----|
| `synchronized` blocks | Pins carrier thread, kills concurrency | Use `ReentrantLock` |
| `ThreadLocal` without cleanup | Memory leaks across millions of threads | Use `ScopedValue` |
| JNI calls | Pin carrier thread | Isolate to platform threads |
| CPU-bound tasks | No blocking = no benefit, extra scheduling overhead | Use platform threads |



