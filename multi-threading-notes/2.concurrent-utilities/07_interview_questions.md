# Java ExecutorService & Multithreading - Interview ## uestions

## ExecutorService Fundamentals

### Basic Concepts

## 1: What is ExecutorService and how does it differ from creating threads directly?
- ExecutorService provides thread pool management, lifecycle control, and task submission framework
- Benefits: Resource management, thread reuse, better performance, easier task management
- Direct thread creation: No reuse, manual lifecycle management, resource intensive

## 2: What are the main methods of ExecutorService interface?
- `submit()` - Submit tasks and get Future
- `execute()` - Execute Runnable tasks
- `invokeAll()` - Execute multiple tasks
- `invokeAny()` - Execute tasks, return first completed
- `shutdown()` - Graceful shutdown
- `shutdownNow()` - Immediate shutdown

## 3: Explain the difference between execute() and submit() methods.
- `execute()`: Takes Runnable, returns void, exceptions are handled by UncaughtExceptionHandler
- `submit()`: Takes Runnable/Callable, returns Future, exceptions wrapped in ExecutionException

### Thread Pool Types

## 4: What are the different types of thread pools provided by Executors class?
- `newFixedThreadPool(n)` - Fixed number of threads
- `newCachedThreadPool()` - Creates threads as needed, reuses existing
- `newSingleThreadExecutor()` - Single worker thread
- `newScheduledThreadPool(n)` - For scheduled/periodic tasks

## 5: When would you use CachedThreadPool vs FixedThreadPool?
- CachedThreadPool: Short-lived asynchronous tasks, unpredictable workload
- FixedThreadPool: Known workload, predictable resource requirements, long-running tasks

## 6: What are the risks of using newCachedThreadPool()?
- Can create unlimited threads leading to resource exhaustion
- No upper bound on thread creation
- Can cause OutOfMemoryError under high load

### Thread Pool Configuration

## 7: Explain ThreadPoolExecutor constructor parameters.
```java
ThreadPoolExecutor(
    int corePoolSize,           // Minimum threads
    int maximumPoolSize,        // Maximum threads
    long keepAliveTime,         // Idle thread timeout
    TimeUnit unit,              // Time unit
    Blocking queue<Runnable> workqueue,  // Task ## ueue
    ThreadFactory threadFactory,         // Thread creation
    RejectedExecutionHandler handler     // Rejection policy
)
```

## 8: How do you determine the optimal thread pool size?
- CPU-intensive: Number of CPU cores
- I/O-intensive: CPU cores × 2 (or higher based on I/O wait time)
- Mixed workload: Re## uires profiling and testing
- Formula: `Threads = CPU_cores × (1 + Wait_time/Service_time)`

## 9: What are the different types of Blocking queue used in thread pools?
- `LinkedBlocking queue` - Unbounded, can cause memory issues
- `ArrayBlocking queue` - Bounded, blocks when full
- `Synchronous queue` - Direct handoff, no storage
- `PriorityBlocking queue` - Priority-based task execution

## 10: Explain rejection policies in ThreadPoolExecutor.
- `AbortPolicy` (default) - Throws RejectedExecutionException
- `CallerRunsPolicy` - Runs task in calling thread
- `DiscardPolicy` - Silently discards task
- `DiscardOldestPolicy` - Discards oldest unhandled task

## Future and Callable

## 11: What is the difference between Runnable and Callable?
- Runnable: `run()` method, returns void, cannot throw checked exceptions
- Callable: `call()` method, returns value, can throw checked exceptions

## 12: How do you handle exceptions in Callable tasks?
```java
Future<String> future = executor.submit(() -> {
    throw new RuntimeException("Task failed");
});

try {
    String result = future.get();
} catch (ExecutionException e) {
    Throwable cause = e.getCause(); // Original exception
}
```

## 13: What happens when you call Future.get() without timeout?
- Blocks indefinitely until task completes
- Can lead to thread starvation
- Better to use `get(timeout, unit)` for responsive applications

## 14: How do you cancel a running task?
```java
Future<?> future = executor.submit(longRunningTask);
boolean cancelled = future.cancel(true); // true = interrupt if running
```

## 15: What is CompletableFuture and how does it differ from Future?
- CompletableFuture: Composable, supports chaining, callbacks, exception handling
- Future: Basic async result container, limited functionality
- CompletableFuture allows non-blocking operations with `thenApply()`, `thenCompose()`

## Exception Handling

### InterruptedException

## 16: What is InterruptedException and how should you handle it?
- Thrown when thread is interrupted during blocking operations
- Correct handling: Restore interrupt flag and exit gracefully
```java
try {
    Thread.sleep(1000);
} catch (InterruptedException e) {
    Thread.currentThread().interrupt(); // Restore flag
    return; // Exit method
}
```

## 17: Why should you restore the interrupt flag after catching InterruptedException?
- Preserves interrupt status for higher-level code
- Allows proper cleanup and shutdown procedures
- Maintains thread's interrupted state for other operations

## 18: What's wrong with this code?
```java
try {
    Thread.sleep(1000);
} catch (InterruptedException e) {
    // Log and continue - WRONG!
}
```
- Swallows interrupt signal
- Thread continues execution ignoring interruption
- Should restore interrupt flag: `Thread.currentThread().interrupt()`

### ExecutorService Exceptions

## 19: What is ExecutionException and when does it occur?
- Wraps exceptions thrown by Callable/Runnable tasks
- Occurs when calling `Future.get()` on failed task
- Use `getCause()` to access original exception

## 20: When do you get RejectedExecutionException?
- Executor is shutdown
- Thread pool is saturated and queue is full
- Task rejected by rejection policy

## 21: How do you handle TimeoutException in Future.get()?
```java
try {
    String result = future.get(5, TimeUnit.SECONDS);
} catch (TimeoutException e) {
    future.cancel(true); // Cancel the task
    // Handle timeout scenario
}
```

## 22: What is CompletionException in CompletableFuture?
- Unchecked exception that wraps underlying exceptions
- Thrown by `join()` method
- Handle using `handle()`, `exceptionally()`, or `whenComplete()`

## Lifecycle Management

## 23: Why is it important to shutdown ExecutorService?
- Prevents resource leaks
- Non-daemon threads keep JVM alive
- Allows graceful completion of pending tasks

## 24: What's the difference between shutdown() and shutdownNow()?
- `shutdown()`: Graceful shutdown, waits for running tasks to complete
- `shutdownNow()`: Immediate shutdown, interrupts running tasks, returns pending tasks

## 25: Write code for proper ExecutorService shutdown.
```java
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
```

## Advanced Topics

## 26: How do you implement a custom ThreadFactory?
```java
ThreadFactory factory = r -> {
    Thread t = new Thread(r);
    t.setName("CustomWorker-" + t.getId());
    t.setDaemon(true);
    t.setUncaughtExceptionHandler((thread, ex) -> {
        System.err.println("Uncaught exception: " + ex.getMessage());
    });
    return t;
};
```

## 27: What is the difference between submit() and invokeAll()?
- `submit()`: Single task, returns Future immediately
- `invokeAll()`: Multiple tasks, returns List<Future>, waits for all to complete

## 28: How do you handle partial failures in invokeAll()?
```java
List<Future<String>> futures = executor.invokeAll(tasks);
for (Future<String> future : futures) {
    try {
        String result = future.get();
        // Process successful result
    } catch (ExecutionException e) {
        // Handle individual task failure
    }
}
```

## 29: What is ForkJoinPool and when would you use it?
- Specialized ExecutorService for divide-and-con## uer algorithms
- Work-stealing algorithm for better load balancing
- Good for recursive tasks, parallel streams

## 30: How do you implement timeout for a group of tasks?
```java
List<Future<String>> futures = executor.invokeAll(tasks, 10, TimeUnit.SECONDS);
// Tasks that don't complete within 10 seconds are cancelled
```

## Practical Scenarios

## 31: How would you implement a producer-consumer pattern with ExecutorService?
```java
Blocking## ueue<String> ## ueue = new LinkedBlocking## ueue<>();
ExecutorService executor = Executors.newFixedThreadPool(4);

// Producer
executor.submit(() -> {
    ## ueue.offer("item");
});

// Consumer
executor.submit(() -> {
    String item = ## ueue.take(); // Blocks until available
});
```

## 32: How do you implement parallel processing of a list?
```java
List<CompletableFuture<String>> futures = items.stream()
    .map(item -> CompletableFuture.supplyAsync(() -> process(item), executor))
    .collect(Collectors.toList());

List<String> results = futures.stream()
    .map(CompletableFuture::join)
    .collect(Collectors.toList());
```

## 33: What are the common pitfalls in multithreading?
- Forgetting to shutdown ExecutorService
- Swallowing InterruptedException
- Using unbounded ## ueues leading to memory issues
- Not handling ExecutionException properly
- Creating too many threads for CPU-intensive tasks

## 34: How do you test multithreaded code?
- Use CountDownLatch for synchronization
- Test with different thread pool sizes
- Use stress testing with high concurrency
- Test timeout scenarios
- Verify proper exception handling

## 35: Explain the difference between CancellationException and InterruptedException.
- CancellationException: Future was cancelled, unchecked exception
- InterruptedException: Thread was interrupted during blocking operation, checked exception

## Performance and Best Practices

## 36: How do you monitor thread pool performance?
- Use ThreadPoolExecutor methods: `getActiveCount()`, `getCompletedTaskCount()`, `getPoolSize()`
- JMX monitoring
- Custom metrics collection
- Thread dumps analysis

## 37: What causes thread pool starvation?
- Tasks submitting other tasks to same pool
- Long-running tasks blocking worker threads
- Insufficient thread pool size
- Dependencies between tasks in same pool

## 38: How do you avoid deadlocks in thread pools?
- Avoid nested task submission to same pool
- Use timeout on blocking operations
- Proper lock ordering
- Use concurrent collections
- Minimize lock scope

## 39: When would you use a custom RejectedExecutionHandler?
- Logging rejected tasks
- ## ueuing tasks in external storage
- Implementing backpressure mechanisms
- Custom retry logic

## 40: What is the difference between parallelStream() and ExecutorService?
- parallelStream(): Uses common ForkJoinPool, automatic parallelization
- ExecutorService: Custom thread pool configuration, explicit task management
- parallelStream() easier but less control, ExecutorService more flexible

## Code Review questions

## 41: What's wrong with this shutdown code?
```java
executor.shutdown();
executor.awaitTermination(1, TimeUnit.SECONDS);
```
Issue: Doesn't check return value of awaitTermination(), no shutdownNow() call

## 42: Identify the problem:
```java
for (int i = 0; i < 1000; i++) {
    ExecutorService exec = Executors.newSingleThreadExecutor();
    exec.submit(() -> doWork());
    // Missing shutdown - resource leak!
}
```

## 43: What could go wrong here?
```java
ExecutorService executor = Executors.newCachedThreadPool();
for (int i = 0; i < 1000000; i++) {
    executor.submit(() -> Thread.sleep(60000));
}
```
Issue: Could create millions of threads, leading to OutOfMemoryError

These questions cover the fundamental to advanced concepts of ExecutorService and multithreading exceptions. Practice implementing the code examples and understanding the underlying concepts for thorough preparation.