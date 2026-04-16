# Java Concurrency Exceptions — Reference Guide


## Quick Reference

| Exception | Package | Checked? | Common Cause | Fix |
|---|---|---|---|---|
| `InterruptedException` | `java.lang` | Yes | Thread interrupted during sleep/wait/join | Restore interrupt flag; exit gracefully |
| `ExecutionException` | `java.util.concurrent` | Yes | Task submitted to executor threw an exception | Unwrap with `getCause()` |
| `TimeoutException` | `java.util.concurrent` | Yes | `Future.get(timeout)` exceeded wait time | Cancel task or extend timeout |
| `RejectedExecutionException` | `java.util.concurrent` | No | Executor shut down or thread pool saturated | Proper shutdown sequencing; configure rejection policy |
| `CompletionException` | `java.util.concurrent` | No | Exception in a `CompletableFuture` chain | Use `handle()` or `exceptionally()` |
| `CancellationException` | `java.util.concurrent` | No | Accessing result of a cancelled `Future` | Check `isCancelled()` before `get()` |
| `IllegalThreadStateException` | `java.lang` | No | Starting a thread that is already started | Check thread state before calling `start()` |
| `IllegalMonitorStateException` | `java.lang` | No | `wait()`/`notify()` called without owning the monitor | Only call inside a `synchronized` block on the same object |
| `BrokenBarrierException` | `java.util.concurrent` | Yes | `CyclicBarrier` broken or reset while threads wait | Indicates a serious sync problem; handle and abort |

---

## Thread Lifecycle Exceptions

### InterruptedException (Checked)

Thrown when a thread is blocked (sleeping, waiting, joining) and another thread calls `interrupt()` on it.

**Correct handling — always restore the interrupt flag:**

```java
try {
    Thread.sleep(1000);
} catch (InterruptedException e) {
    Thread.currentThread().interrupt(); // restore the flag
    return; // exit gracefully
}
```

**Why restore the flag?** Catching `InterruptedException` clears the interrupt status. If you don't restore it, upstream code that checks `Thread.isInterrupted()` will never know the thread was interrupted.

**Wrong — swallowing the exception:**

```java
// BAD — interrupt is lost; thread keeps running indefinitely
try { Thread.sleep(1000); }
catch (InterruptedException e) { /* ignoring */ }
```

---

### IllegalThreadStateException (Unchecked)

Thrown when `Thread.start()` is called on a thread that has already been started (even if it has finished).

```java
Thread t = new Thread(() -> System.out.println("running"));
t.start();
t.start(); // IllegalThreadStateException — cannot restart a thread
```

Create a new `Thread` instance each time you need to run it.

---

### IllegalMonitorStateException (Unchecked)

Thrown when `wait()`, `notify()`, or `notifyAll()` is called on an object whose monitor the current thread does not own.

```java
// BAD — not inside synchronized block
obj.wait(); // IllegalMonitorStateException

// GOOD
synchronized (obj) {
    obj.wait(); // current thread owns the monitor
}
```

---

## ExecutorService & Future Exceptions

### ExecutionException (Checked)

Wraps any exception thrown by a `Callable` or `Runnable` submitted to an executor. Always unwrap with `getCause()`.

```java
Future<String> future = executor.submit(() -> {
    throw new IOException("disk error");
});

try {
    String result = future.get();
} catch (ExecutionException e) {
    Throwable cause = e.getCause(); // IOException
    System.out.println("Task failed: " + cause.getMessage());
} catch (InterruptedException e) {
    Thread.currentThread().interrupt();
}
```

---

### TimeoutException (Checked)

Thrown by `Future.get(long, TimeUnit)` when the task does not complete within the specified time.

```java
try {
    String result = future.get(5, TimeUnit.SECONDS);
} catch (TimeoutException e) {
    System.out.println("Task timed out");
    future.cancel(true); // interrupt the task
} catch (ExecutionException e) {
    // task threw an exception
} catch (InterruptedException e) {
    Thread.currentThread().interrupt();
}
```

---

### RejectedExecutionException (Unchecked)

Thrown when an `ExecutorService` cannot accept a new task — either because it has been shut down or because its queue and thread pool are both at capacity.

```java
ExecutorService executor = Executors.newFixedThreadPool(2);
executor.shutdown();
executor.submit(() -> {}); // RejectedExecutionException
```

Configure a rejection policy on `ThreadPoolExecutor` to control behaviour:

| Policy | Behaviour |
|---|---|
| `AbortPolicy` (default) | Throws `RejectedExecutionException` |
| `CallerRunsPolicy` | Runs the task on the calling thread (back-pressure) |
| `DiscardPolicy` | Silently drops the task |
| `DiscardOldestPolicy` | Drops the oldest queued task and retries |

---

## CompletableFuture Exceptions

### CompletionException (Unchecked)

Thrown by `CompletableFuture.join()` (or re-thrown by `get()` as `ExecutionException`) when a stage in the pipeline fails. It wraps the original exception.

```java
CompletableFuture<String> future = CompletableFuture
    .supplyAsync(() -> { throw new RuntimeException("failed"); })
    .exceptionally(ex -> {
        System.out.println("Caught: " + ex.getMessage());
        return "fallback";
    });

String result = future.join(); // "fallback" — no exception thrown
```

**`get()` vs `join()` for exception surfacing:**

| Method | Wraps Exception In |
|---|---|
| `get()` | `ExecutionException` (checked) |
| `join()` | `CompletionException` (unchecked) |

Both carry the original cause inside them.

---

### CancellationException (Unchecked)

Thrown when calling `get()` or `join()` on a `Future` that has been cancelled.

```java
Future<String> future = executor.submit(() -> "hello");
future.cancel(true);

if (!future.isCancelled()) {
    String result = future.get(); // safe
}
// or handle it:
try {
    future.get();
} catch (CancellationException e) {
    System.out.println("Task was cancelled");
}
```

---

## Concurrency Utilities Exceptions

### BrokenBarrierException (Checked)

Thrown when a `CyclicBarrier` is broken (due to a thread being interrupted or timing out) while other threads are still waiting at it.

```java
CyclicBarrier barrier = new CyclicBarrier(3);
try {
    barrier.await();
} catch (BrokenBarrierException e) {
    System.out.println("Barrier broken — another thread failed");
} catch (InterruptedException e) {
    Thread.currentThread().interrupt();
}
```

A broken barrier indicates a coordination failure. The recommended response is to abort the operation and log the failure.

---

## Full Exception Handling Pattern — ExecutorService

```java
ExecutorService executor = Executors.newFixedThreadPool(4);
try {
    Future<String> future = executor.submit(() -> {
        // may throw
        return "result";
    });

    String result = future.get(10, TimeUnit.SECONDS);
    System.out.println(result);

} catch (ExecutionException e) {
    System.err.println("Task failed: " + e.getCause().getMessage());

} catch (TimeoutException e) {
    System.err.println("Task timed out");
    // future.cancel(true) here if you have the reference

} catch (InterruptedException e) {
    Thread.currentThread().interrupt();
    System.err.println("Interrupted while waiting");

} finally {
    executor.shutdown();
    try {
        if (!executor.awaitTermination(60, TimeUnit.SECONDS)) {
            executor.shutdownNow();
        }
    } catch (InterruptedException e) {
        executor.shutdownNow();
        Thread.currentThread().interrupt();
    }
}
```

---

## Full Exception Handling Pattern — CompletableFuture

```java
CompletableFuture<String> future = CompletableFuture
    .supplyAsync(() -> {
        if (Math.random() > 0.5) throw new RuntimeException("random failure");
        return "success";
    })
    .handle((result, ex) -> {
        if (ex != null) {
            System.err.println("Handled: " + ex.getMessage());
            return "default";
        }
        return result;
    });

String result = future.join(); // safe — handle() absorbs the exception
```

---

## Exception Handling Method Comparison (CompletableFuture)

| Method | Triggers On | Transforms Result? | Can Return Fallback? |
|---|---|---|---|
| `exceptionally(fn)` | Failure only | Yes (returns fallback) | Yes |
| `handle(fn)` | Always | Yes | Yes |
| `whenComplete(fn)` | Always | No (pass-through) | No |

---

## Best Practices

1. **Always restore the interrupt flag** after catching `InterruptedException` — `Thread.currentThread().interrupt()`
2. **Always unwrap `ExecutionException`** — the real exception is in `getCause()`
3. **Always use `Future.get(timeout, unit)`** in production — never block indefinitely
4. **Always shut down `ExecutorService`** — omitting it causes thread leaks and JVM not exiting
5. **Use `handle()` over `exceptionally()`** when you need to inspect both the result and exception
6. **Check `isCancelled()` before `get()`** when cancellation is possible
7. **Configure a rejection policy** on `ThreadPoolExecutor` — the default `AbortPolicy` crashes the caller silently if not caught
