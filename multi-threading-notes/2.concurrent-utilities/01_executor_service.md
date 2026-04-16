# ExecutorService — Reference Guide

## What It Is

`ExecutorService` is a high-level interface in `java.util.concurrent` that manages a pool of worker threads. It extends `Executor` and adds lifecycle management, bulk task execution, and `Future`-based result tracking.

**Key benefits:** thread pool management, task submission, lifecycle control, resource management.

---

## Core API

### Task Submission

```java
<T> Future<T> submit(Callable<T> task)       // returns result
    Future<?> submit(Runnable task)           // returns null from Future.get()
<T> Future<T> submit(Runnable task, T result) // returns given result from Future.get()
      void    execute(Runnable command)        // fire-and-forget, no Future
```

### Bulk Execution

```java
// Block until all tasks complete, return all Futures
<T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks)
<T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks, long timeout, TimeUnit unit)

// Block until any task completes, return that result (cancels remaining)
<T> T invokeAny(Collection<? extends Callable<T>> tasks)
<T> T invokeAny(Collection<? extends Callable<T>> tasks, long timeout, TimeUnit unit)
```

---

## Runnable vs Callable

| | `Runnable` | `Callable<V>` |
|---|---|---|
| Method | `run()` | `call()` |
| Returns | `void` | `V` |
| Throws checked exception | No | Yes |
| Works with `Thread` | Yes | No |
| Works with `ExecutorService` | Yes | Yes |
| Result via | — | `Future<V>` |
| Since | Java 1.0 | Java 5 |

Use **Runnable** for fire-and-forget (logging, notifications).  
Use **Callable** when you need a result or need to propagate a checked exception.

### Examples

```java
// Runnable — no result
executor.submit(() -> System.out.println("Runnable on: " + Thread.currentThread().getName()));

// Callable — returns result
Future<Integer> future = executor.submit(() -> 42);
System.out.println(future.get()); // 42

// Callable — throws checked exception (allowed)
Callable<Integer> risky = () -> {
    throw new IOException("disk error");
};
```

---

## Future API

| Method | Description |
|---|---|
| `V get()` | Blocks until result is available; throws `ExecutionException` if task failed |
| `V get(long timeout, TimeUnit unit)` | Blocks up to timeout; throws `TimeoutException` if exceeded |
| `boolean cancel(boolean mayInterruptIfRunning)` | Attempts cancellation; returns `false` if already done |
| `boolean isCancelled()` | `true` if cancelled before completion |
| `boolean isDone()` | `true` if finished (success, exception, or cancelled) |

**Critical:** `future.get()` **blocks the calling thread** — always use the timeout overload in production:

```java
try {
    String result = future.get(5, TimeUnit.SECONDS);
} catch (TimeoutException e) {
    future.cancel(true);
} catch (ExecutionException e) {
    Throwable cause = e.getCause(); // original exception from the task
} catch (InterruptedException e) {
    Thread.currentThread().interrupt(); // always restore the flag
}
```

---

## Thread Pool Implementations

### Factory Methods (Executors utility class)

| Factory Method | Core / Max Threads | Queue | Keep-Alive | Best For |
|---|---|---|---|---|
| `newFixedThreadPool(n)` | n / n | Unbounded `LinkedBlockingQueue` | No | Known, stable workloads |
| `newCachedThreadPool()` | 0 / `Integer.MAX_VALUE` | `SynchronousQueue` | 60 s | Short-lived, bursty tasks |
| `newSingleThreadExecutor()` | 1 / 1 | Unbounded `LinkedBlockingQueue` | No | Sequential processing |
| `newWorkStealingPool()` | CPU cores (default) | Per-thread deques | N/A | Parallel, recursive tasks |
| `newScheduledThreadPool(n)` | n / `Integer.MAX_VALUE` | `DelayedWorkQueue` | N/A | Delayed / periodic tasks |

**Choosing pool size:**
- CPU-bound tasks: `Runtime.getRuntime().availableProcessors()`
- I/O-bound tasks: `availableProcessors() * 2` (or higher — benchmark your workload)

---

### ThreadPoolExecutor — Full Control

```java
ThreadPoolExecutor executor = new ThreadPoolExecutor(
    2,                              // corePoolSize
    10,                             // maximumPoolSize
    60L,                            // keepAliveTime (for threads above core)
    TimeUnit.SECONDS,
    new ArrayBlockingQueue<>(100),  // bounded queue (prefer over unbounded)
    r -> {
        Thread t = new Thread(r);
        t.setDaemon(true);
        t.setName("worker-" + t.getId());
        return t;
    },
    new ThreadPoolExecutor.CallerRunsPolicy() // rejection policy
);
```

**Queue choices:**

| Queue | Bounded? | Notes |
|---|---|---|
| `LinkedBlockingQueue` | No (by default) | Risk of OOM under load |
| `ArrayBlockingQueue(n)` | Yes | Recommended for production |
| `SynchronousQueue` | — | Direct handoff; used by cached pool |
| `PriorityBlockingQueue` | No | Task prioritization |

**Rejection policies:**

| Policy | Behaviour |
|---|---|
| `AbortPolicy` (default) | Throws `RejectedExecutionException` |
| `CallerRunsPolicy` | Runs task in the submitting thread (natural back-pressure) |
| `DiscardPolicy` | Silently drops the task |
| `DiscardOldestPolicy` | Drops the oldest queued task, retries submission |

---

## Lifecycle Management

### Shutdown Methods

| Method | Running Tasks | Queued Tasks | Returns |
|---|---|---|---|
| `shutdown()` | Let complete | Will execute | `void` |
| `shutdownNow()` | Interrupt attempts | Returned as list | `List<Runnable>` |
| `isShutdown()` | — | — | `boolean` |
| `isTerminated()` | — | — | `boolean` |
| `awaitTermination(timeout, unit)` | Waits up to timeout | — | `boolean` |

**`awaitTermination` does not initiate shutdown — call `shutdown()` or `shutdownNow()` first.**

### Correct Shutdown Pattern

```java
ExecutorService executor = Executors.newFixedThreadPool(4);
try {
    executor.submit(() -> { /* task */ });
} finally {
    executor.shutdown();
    try {
        if (!executor.awaitTermination(60, TimeUnit.SECONDS)) {
            executor.shutdownNow();
            if (!executor.awaitTermination(60, TimeUnit.SECONDS)) {
                System.err.println("Executor did not terminate");
            }
        }
    } catch (InterruptedException e) {
        executor.shutdownNow();
        Thread.currentThread().interrupt();
    }
}
```

### Java 19+ Try-with-Resources

```java
try (ExecutorService executor = Executors.newFixedThreadPool(4)) {
    executor.submit(() -> { /* task */ });
} // auto-shutdown on exit
```

---

## Scheduled Tasks

```java
ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(2);

// One-shot after delay
scheduler.schedule(() -> System.out.println("after 3 s"), 3, TimeUnit.SECONDS);

// Fixed-rate: next run starts at (start + period) regardless of task duration
ScheduledFuture<?> f = scheduler.scheduleAtFixedRate(
    () -> System.out.println("fixed rate"),
    1, 3, TimeUnit.SECONDS
);

// Fixed-delay: next run starts at (completion + delay)
scheduler.scheduleWithFixedDelay(
    () -> System.out.println("fixed delay"),
    1, 3, TimeUnit.SECONDS
);

// Cancel periodic task
f.cancel(true);
```

| Type | Next Run Starts After | Task Takes Longer Than Period? |
|---|---|---|
| `scheduleAtFixedRate` | Period elapses from start | Queued; runs immediately when thread is free |
| `scheduleWithFixedDelay` | Previous completion + delay | Always waits full delay |

---

## ForkJoinPool and Work Stealing

Designed for divide-and-conquer recursive tasks.

- **fork()** — submit a subtask to the work-stealing deque
- **join()** — wait for subtask and return result
- **Work stealing** — idle threads steal tasks from the back of busy threads' deques

Default parallelism = `Runtime.getRuntime().availableProcessors()`.

```java
ForkJoinPool pool = ForkJoinPool.commonPool();
Future<Integer> result = pool.submit(new SumTask(0, 100));
System.out.println(result.get());

class SumTask extends RecursiveTask<Integer> {
    final int start, end;

    SumTask(int start, int end) {
        this.start = start;
        this.end = end;
    }

    @Override
    protected Integer compute() {
        if (end - start <= 4) {
            int sum = 0;
            for (int i = start; i < end; i++) sum += i;
            return sum;
        }
        int mid = (start + end) / 2;
        SumTask left  = new SumTask(start, mid);
        SumTask right = new SumTask(mid, end);
        left.fork();                   // async
        int rightResult = right.compute(); // current thread
        return left.join() + rightResult;
    }
}
```

| Class | Returns Value? | Use When |
|---|---|---|
| `RecursiveTask<T>` | Yes | Subtasks produce results |
| `RecursiveAction` | No | Subtasks perform side-effects only |

---

## Common Patterns

### Parallel Processing + Collect Results

```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5);
ExecutorService executor = Executors.newFixedThreadPool(4);

List<Future<Integer>> futures = numbers.stream()
    .map(n -> executor.submit(() -> n * n))
    .toList();

List<Integer> results = futures.stream()
    .map(f -> {
        try { return f.get(); }
        catch (Exception e) { throw new RuntimeException(e); }
    })
    .toList();

executor.shutdown();
```

### Producer-Consumer

```java
BlockingQueue<String> queue = new LinkedBlockingQueue<>();

executor.submit(() -> {
    for (int i = 0; i < 10; i++) queue.offer("item-" + i);
});

executor.submit(() -> {
    while (true) {
        String item = queue.poll(1, TimeUnit.SECONDS);
        if (item != null) process(item);
    }
});
```

### Task with Timeout

```java
Future<String> future = executor.submit(() -> {
    Thread.sleep(5000);
    return "done";
});

try {
    String result = future.get(2, TimeUnit.SECONDS);
} catch (TimeoutException e) {
    System.out.println("timed out, cancelling");
    future.cancel(true);
}
```

---

## Common Pitfalls

### 1. Forgetting to Shutdown (Resource Leak)

```java
// BAD — threads stay alive, application may not exit
ExecutorService ex = Executors.newFixedThreadPool(4);
ex.submit(() -> {});

// GOOD — always shut down in finally or try-with-resources
```

### 2. Swallowing InterruptedException

```java
// BAD
try { result = future.get(); }
catch (InterruptedException e) { /* ignoring */ }

// GOOD
try { result = future.get(); }
catch (InterruptedException e) {
    Thread.currentThread().interrupt(); // restore flag
    throw new RuntimeException("interrupted", e);
}
```

### 3. Unbounded Queue (OOM Risk)

```java
// BAD — LinkedBlockingQueue with no bound
new ThreadPoolExecutor(1, 1, 0L, MILLISECONDS, new LinkedBlockingQueue<>());

// GOOD — bounded queue with explicit rejection policy
new ThreadPoolExecutor(1, 1, 0L, MILLISECONDS,
    new ArrayBlockingQueue<>(100),
    new ThreadPoolExecutor.CallerRunsPolicy());
```

### 4. Submitting After shutdown()

```java
executor.shutdown();
executor.submit(() -> {}); // throws RejectedExecutionException
```

---

## Interview Quick Reference

**`shutdown()` vs `shutdownNow()`?**
`shutdown()` is graceful — running tasks finish, queued tasks execute. `shutdownNow()` attempts to interrupt running tasks and returns queued tasks as a list.

**Does `awaitTermination()` stop the executor?**
No. It only blocks until termination completes (or timeout expires). You must call `shutdown()` first.

**`scheduleAtFixedRate` vs `scheduleWithFixedDelay`?**
Fixed-rate measures from the start of the previous run. Fixed-delay measures from the completion of the previous run.

**`Future` vs `CompletableFuture`?**
`Future` is basic — get result or cancel. `CompletableFuture` supports chaining, composition, async pipelines, and explicit completion.

**When to use which pool?**

| Pool | Use When |
|---|---|
| `FixedThreadPool` | Predictable, bounded workload |
| `CachedThreadPool` | Many short-lived, bursty tasks |
| `SingleThreadExecutor` | Strict sequential execution |
| `WorkStealingPool` / `ForkJoinPool` | Recursive, divide-and-conquer problems |
| `ScheduledThreadPool` | Delayed or periodic tasks |
