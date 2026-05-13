```java id="guzw36"
Callable<String> task = () -> {
    throw new RuntimeException("User not found");
};

ExecutorService executor = Executors.newSingleThreadExecutor();

Future<String> future = executor.submit(task);

try {
    future.get();
} catch (ExecutionException e) {
    System.out.println(e.getCause());
}
```

## Short Notes

* `Callable` task runs in separate thread.
* Exception occurs inside worker thread.
* Exception does NOT directly come to main thread.
* `future.get()` waits for task completion.
* Java wraps task exception inside:

```java id="4w5r4g"
ExecutionException
```

* Actual exception is retrieved using:

```java id="c8jcrv"
e.getCause()
```

---

## Flow

```text id="ez3b94"
Callable task
    ↓
RuntimeException occurs
    ↓
Executor catches exception
    ↓
Stores inside Future
    ↓
future.get()
    ↓
throws ExecutionException
    ↓
getCause() returns original exception
```

---

## Output

```text id="1f9m8q"
java.lang.RuntimeException: User not found
```

---

## Interview Point

```text id="s1u9po"
ExecutionException is wrapper exception.
Actual business exception is inside getCause().
```



<br/>
<br/><br/><br/>

# CompletionException — Short Note

`CompletionException` is similar to:

```java id="5pf9dh"
ExecutionException
```

Both are wrapper exceptions.

Main difference:

| Exception             | Mostly Used In             |
| --------------------- | -------------------------- |
| `ExecutionException`  | `Future.get()`             |
| `CompletionException` | `CompletableFuture.join()` |

---

# Example

```java id="vpxj8w"
CompletableFuture<String> future =
    CompletableFuture.supplyAsync(() -> {
        throw new RuntimeException("User not found");
    });

try {
    future.join();
} catch (CompletionException e) {
    System.out.println(e.getCause());
}
```

---

# Flow

```text id="x8h25x"
Async task
   ↓
RuntimeException occurs
   ↓
CompletableFuture catches it
   ↓
join()
   ↓
throws CompletionException
   ↓
getCause() gives original exception
```

---

# Output

```text id="1vb3yb"
java.lang.RuntimeException: User not found
```

---

# Why CompletionException Exists?

`CompletableFuture` heavily uses:

* chaining
* async pipelines
* functional style

So Java uses:

```java id="l6x9tw"
CompletionException
```

for propagating exceptions through async stages.

---

# Important Difference

## get()

```java id="flf76d"
future.get()
```

* checked exception
* must handle using try-catch

Throws:

```java id="k7nmtw"
ExecutionException
InterruptedException
```

---

## join()

```java id="6lt08m"
future.join()
```

* unchecked exception
* no mandatory try-catch

Throws:

```java id="kpxwxy"
CompletionException
```

---

# Interview Summary

| Method                     | Wrapper Exception     | Checked? |
| -------------------------- | --------------------- | -------- |
| `Future.get()`             | `ExecutionException`  | Yes      |
| `CompletableFuture.join()` | `CompletionException` | No       |

---

# Easy Memory Trick

```text id="pr6a5u"
get()  → checked wrapper
join() → unchecked wrapper
```
