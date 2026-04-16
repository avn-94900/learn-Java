# Java Threading — Concepts & Patterns

## Thread Creation Methods

### 1. Extending Thread
```java
class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("Running: " + Thread.currentThread().getName());
    }
}

new MyThread().start(); // ✅ new thread
// new MyThread().run(); ❌ runs in main thread
```
Drawback: can't extend another class (no multiple inheritance).

---

### 2. Implementing Runnable
```java
class MyRunnable implements Runnable {
    @Override
    public void run() {
        System.out.println("Running: " + Thread.currentThread().getName());
    }
}

new Thread(new MyRunnable()).start();
```
Preferred over `extends Thread` — allows extending other classes.

---

### 3. Lambda (Java 8+)
```java
Runnable task = () -> System.out.println("Running: " + Thread.currentThread().getName());
new Thread(task).start();

// Inline
new Thread(() -> System.out.println("Inline lambda")).start();
```

---

### 4. ExecutorService (Thread Pool)
```java
ExecutorService executor = Executors.newFixedThreadPool(3);

executor.submit(() -> System.out.println("Task 1: " + Thread.currentThread().getName()));
executor.execute(() -> System.out.println("Task 2: " + Thread.currentThread().getName()));

executor.shutdown(); // always shutdown
```

**Pool types:**
```java
Executors.newSingleThreadExecutor()       // 1 thread
Executors.newFixedThreadPool(n)           // fixed n threads
Executors.newCachedThreadPool()           // grows as needed
Executors.newScheduledThreadPool(n)       // for scheduled tasks
```

---

### 5. Callable + Future
```java
ExecutorService executor = Executors.newSingleThreadExecutor();

Callable<String> task = () -> {
    Thread.sleep(2000);
    return "Result from: " + Thread.currentThread().getName();
};

Future<String> future = executor.submit(task);
System.out.println("Task submitted...");

String result = future.get(); // blocks until done
System.out.println("Result: " + result);

executor.shutdown();
```

---

### 6. CompletableFuture (Java 8+)
```java
CompletableFuture<String> future = CompletableFuture
    .supplyAsync(() -> "hello from " + Thread.currentThread().getName())
    .thenApply(String::toUpperCase);

System.out.println(future.get());
```
Use for complex async workflows and chaining.

---

### Summary

| Method | New Thread | Return Value | Best For |
|--------|-----------|--------------|----------|
| `extends Thread` | Yes | No | Simple tasks |
| `implements Runnable` | Yes | No | Most common |
| Lambda | Yes | No | Modern concise code |
| `ExecutorService` | Yes | With Callable | Production apps |
| `Callable + Future` | Yes | Yes | Tasks needing results |
| `CompletableFuture` | Yes | Yes | Async workflows |

| Call | New Thread? | Note |
|------|------------|------|
| `thread.start()` | Yes | Correct |
| `thread.run()` | No | Runs in current thread |
| `executor.submit()` | Yes | Returns `Future` |
| `executor.execute()` | Yes | Fire and forget |

---

## sleep() vs wait()

| Feature | `sleep()` | `wait()` |
|---------|-----------|----------|
| Defined in | `Thread` class | `Object` class |
| Purpose | Pause for a duration | Wait for a condition/signal |
| Requires `synchronized`? | No | Yes |
| Releases lock? | No | Yes |
| Resumes when | Timer expires | `notify()` / `notifyAll()` called |
| Thread state | `TIMED_WAITING` | `WAITING` or `TIMED_WAITING` |

**sleep() — fixed pause:**
```java
System.out.println("Sleeping...");
Thread.sleep(2000);
System.out.println("Awake.");
```

**wait() / notify() — conditional pause:**
```java
// Thread A — waits
synchronized (lock) {
    lock.wait(); // releases lock, pauses
}

// Thread B — signals
synchronized (lock) {
    lock.notify(); // wakes one waiting thread
}
```

Analogy:
- `sleep()` → set an alarm, no one can wake you early
- `wait()` → waiting for someone to tap your shoulder

---

## ThreadLocal

Each thread gets its own isolated copy of the variable. One `ThreadLocal` object is shared, but each thread sees only its own value.

**Basic usage:**
```java
ThreadLocal<String> threadLocal = new ThreadLocal<>();

threadLocal.set(Thread.currentThread().getName());
System.out.println("Main: " + threadLocal.get()); // main

Thread t1 = new Thread(() -> {
    threadLocal.set(Thread.currentThread().getName());
    System.out.println("T1: " + threadLocal.get()); // Thread-0
});
t1.start();
```

**How it works internally:**
- Each `Thread` object holds a `ThreadLocalMap`.
- `set(value)` → stores value in the current thread's map under the `ThreadLocal` key.
- `get()` → looks up value from the current thread's own map.

**Thread pool cleanup — always call `remove()`:**

When threads are reused in a pool, the next task on the same thread may see the previous task's value if you don't clean up.

```java
ExecutorService pool = Executors.newFixedThreadPool(2);
ThreadLocal<String> threadLocal = new ThreadLocal<>();

pool.submit(() -> {
    threadLocal.set("Task-1 value");
    System.out.println("Task-1: " + threadLocal.get());
    threadLocal.remove(); // ✅ always clean up
});

pool.submit(() -> {
    System.out.println("Task-2: " + threadLocal.get()); // null if removed correctly
});
```

---

## ThreadPoolExecutor + Future

```java
public static void main(String[] args) {
    ThreadPoolExecutor poolExecutor = new ThreadPoolExecutor(
        1, 1, 1, TimeUnit.HOURS,
        new ArrayBlockingQueue<>(10),
        Executors.defaultThreadFactory(),
        new ThreadPoolExecutor.AbortPolicy()
    );

    Future<?> future = poolExecutor.submit(() -> {
        System.out.println("Task running in: " + Thread.currentThread().getName());
        Thread.sleep(7000);
    });

    System.out.println("isDone: " + future.isDone()); // false

    try {
        future.get(2, TimeUnit.SECONDS); // wait max 2s
    } catch (TimeoutException e) {
        System.out.println("TimeoutException: task still running.");
    } catch (Exception e) {
        e.printStackTrace();
    }

    try {
        future.get(); // wait indefinitely
        System.out.println("isDone: " + future.isDone());       // true
        System.out.println("isCancelled: " + future.isCancelled()); // false
    } catch (Exception e) {
        e.printStackTrace();
    }

    poolExecutor.shutdown();
}
```

**Key `Future` methods:**

| Method | Description |
|--------|-------------|
| `get()` | Blocks until task completes, returns result |
| `get(timeout, unit)` | Blocks up to timeout, throws `TimeoutException` |
| `isDone()` | `true` if completed (normally, cancelled, or exception) |
| `isCancelled()` | `true` if cancelled before completion |
| `cancel(mayInterrupt)` | Attempts to cancel the task |

---


