# CompletableFuture — Reference Guide


## Method Overview

| Method | Input | Returns | Depends on Previous? | Parallel? | Memory Tip |
|---|---|---|---|---|---|
| `thenApply` | One value | `CompletableFuture<T>` | Yes | No | `map` |
| `thenCompose` | One value | `CompletableFuture<T>` | Yes | No | `flatMap` |
| `thenCombine` | Two values | `CompletableFuture<T>` | No | Yes | Parallel merge |
| `thenAccept` | One value | `CompletableFuture<Void>` | Yes | No | No return |
| `thenAcceptBoth` | Two values | `CompletableFuture<Void>` | No | Yes | Parallel consume |
| `allOf` | Many futures | `CompletableFuture<Void>` | No | Yes | Batch wait |
| `anyOf` | Many futures | `CompletableFuture<Object>` | No | Yes | Fastest wins |

---

## thenApply vs thenCompose vs thenCombine

| Feature | `thenApply` | `thenCompose` | `thenCombine` |
|---|---|---|---|
| Function Type | `Function<T, R>` | `Function<T, CompletableFuture<R>>` | `BiFunction<T, U, R>` |
| Return Type | `CF<R>` | `CF<R>` | `CF<R>` |
| Tasks Independent? | No | No | Yes |
| Parallel Execution | No | No | Yes |
| Nested Future Risk | Yes (wrong usage) | No | No |
| Use For | Value transform | Chain async calls | Merge two futures |
| Common Mistake | Passing async method | Overusing when sync suffices | Using for dependent tasks |

---

## Async vs Sync Variants

Every chaining method has an `Async` suffix variant that runs on a different thread (from the common ForkJoinPool or a provided Executor):

| Sync | Async (common pool) | Async (custom executor) |
|---|---|---|
| `thenApply(fn)` | `thenApplyAsync(fn)` | `thenApplyAsync(fn, executor)` |
| `thenCompose(fn)` | `thenComposeAsync(fn)` | `thenComposeAsync(fn, executor)` |
| `thenCombine(cf, fn)` | `thenCombineAsync(cf, fn)` | `thenCombineAsync(cf, fn, executor)` |
| `thenAccept(fn)` | `thenAcceptAsync(fn)` | `thenAcceptAsync(fn, executor)` |

Sync variants run on the completing thread (or the calling thread if already complete). Use `Async` variants to avoid blocking the completing thread.

---

## Code Examples

### Base Setup

```java
import java.util.concurrent.CompletableFuture;

class Services {

    static CompletableFuture<String> getUser() {
        return CompletableFuture.supplyAsync(() -> "User");
    }

    static CompletableFuture<String> getOrders(String user) {
        return CompletableFuture.supplyAsync(() -> "Orders of " + user);
    }

    static CompletableFuture<String> getPayment() {
        return CompletableFuture.supplyAsync(() -> "Payment Info");
    }
}
```

### thenApply — Value Transform

```java
CompletableFuture<String> result =
    Services.getUser()
            .thenApply(String::toUpperCase);

// "User" → "USER"
```

Use when the transformation is a plain function that does **not** return a future.

---

### thenCompose — Dependent Async Chain

```java
CompletableFuture<String> result =
    Services.getUser()
            .thenCompose(Services::getOrders);

// getUser completes first → result passed to getOrders
```

Use when the next step is itself an async call returning a `CompletableFuture`.

---

### thenCombine — Parallel Independent Tasks

```java
CompletableFuture<String> result =
    Services.getUser()
            .thenCombine(
                Services.getPayment(),
                (user, payment) -> user + " | " + payment
            );

// getUser and getPayment run in parallel → merged when both complete
```

---

### thenAccept — Consume Without Returning

```java
Services.getUser()
        .thenAccept(user -> System.out.println("Got: " + user));
```

Returns `CompletableFuture<Void>`. Use for logging, side-effects, notifications.

---

### allOf — Wait for All

```java
CompletableFuture<String> userFuture    = Services.getUser();
CompletableFuture<String> paymentFuture = Services.getPayment();

CompletableFuture.allOf(userFuture, paymentFuture)
    .thenRun(() -> {
        String user    = userFuture.join();    // safe — already complete
        String payment = paymentFuture.join();
        System.out.println(user + " | " + payment);
    });
```

`allOf` returns `CompletableFuture<Void>` — use `.join()` on individual futures inside the callback.

---

### anyOf — First to Complete Wins

```java
CompletableFuture<Object> first =
    CompletableFuture.anyOf(
        Services.getUser(),
        Services.getPayment()
    );

first.thenAccept(result -> System.out.println("First done: " + result));
```

Returns `CompletableFuture<Object>` — cast carefully.

---

## Exception Handling

### exceptionally — Recover from Failure

```java
CompletableFuture<String> result =
    Services.getUser()
            .exceptionally(ex -> {
                System.out.println("Error: " + ex.getMessage());
                return "default-user";
            });
```

Only triggers if the stage completed exceptionally.

---

### handle — Always Runs (Success or Failure)

```java
CompletableFuture<String> result =
    Services.getUser()
            .handle((value, ex) -> {
                if (ex != null) {
                    return "fallback";
                }
                return value;
            });
```

Prefer `handle` over `exceptionally` when you need to inspect both the result and the exception.

---

### whenComplete — Side-Effect on Either Outcome

```java
Services.getUser()
        .whenComplete((value, ex) -> {
            if (ex != null) {
                log.error("Failed", ex);
            } else {
                log.info("Success: {}", value);
            }
        });
```

Does **not** transform the result — passes it through unchanged.

---

## Common Mistakes

### Nested Future Bug — Wrong: thenApply with Async Method

```java
// WRONG — results in CompletableFuture<CompletableFuture<String>>
CompletableFuture<CompletableFuture<String>> nested =
    Services.getUser()
            .thenApply(user -> Services.getOrders(user));

// CORRECT — use thenCompose to unwrap
CompletableFuture<String> flat =
    Services.getUser()
            .thenCompose(user -> Services.getOrders(user));
```

### Blocking with join() Too Early

```java
// BAD — defeats the purpose of async
String user = Services.getUser().join();  // blocks here
String orders = Services.getOrders(user).join();

// GOOD — chain without blocking
Services.getUser()
        .thenCompose(Services::getOrders)
        .thenAccept(System.out::println);
```

### Using thenCombine for Dependent Tasks

```java
// WRONG — payment depends on user, so they cannot run in parallel
userFuture.thenCombine(paymentFuture, (u, p) -> ...);

// CORRECT — use thenCompose when second task needs result of first
Services.getUser()
        .thenCompose(user -> Services.getOrdersForUser(user));
```

| Mistake | Fix |
|---|---|
| `thenApply` with async method | Use `thenCompose` |
| `.join()` blocking mid-chain | Chain with `thenCompose` / `thenApply` |
| Dependent tasks run in parallel | Use `thenCompose` |
| Independent tasks run sequentially | Use `thenCombine` or start both before chaining |
| Swallowing exceptions | Use `handle` or `exceptionally` |

---

## Decision Rules

```
Function returns a plain value        → thenApply
Function returns CompletableFuture    → thenCompose
Two independent tasks, merge results  → thenCombine
Consume result, no return             → thenAccept
Wait for all futures                  → allOf
First result wins                     → anyOf
Recover from failure                  → exceptionally
Always run (success or failure)       → handle
```

Apply in this order when deciding:
1. What does the function **return**? (plain value vs future)
2. Does this step **depend** on the previous result?
3. Can these tasks **run in parallel**?

---

## Interview Questions

**Q: When do you use `thenCompose` instead of `thenApply`?**
When the mapping function itself returns a `CompletableFuture`. `thenApply` would produce a nested `CompletableFuture<CompletableFuture<T>>`.

**Q: What does this return?**
```java
CompletableFuture<Integer> cf = CompletableFuture.supplyAsync(() -> 5);
var result = cf.thenApply(x -> CompletableFuture.completedFuture(x * 2));
```
`CompletableFuture<CompletableFuture<Integer>>` — this is the nested future bug. Fix: use `thenCompose`.

**Q: What is the difference between `handle` and `exceptionally`?**
`exceptionally` only fires on failure and allows returning a fallback. `handle` always fires with `(result, exception)` — one of them will be `null`.

**Q: Does `allOf` give you the results?**
No. `allOf` returns `CompletableFuture<Void>`. Call `.join()` on each individual future inside the `.thenRun()` callback.
