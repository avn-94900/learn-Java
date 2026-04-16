# Java Locks — Part 1: synchronized and ReentrantLock

---

## The Problem with synchronized

`synchronized` locks the **specific object instance**, not the method globally. Two threads operating on two different instances bypass each other's lock entirely.

```java
class CriticalClass {
    synchronized void criticalMethod() {
        System.out.println(Thread.currentThread().getName() + " entered");
        try { Thread.sleep(2000); } catch (InterruptedException ignored) {}
        System.out.println(Thread.currentThread().getName() + " leaving");
    }
}

CriticalClass object1 = new CriticalClass();
CriticalClass object2 = new CriticalClass();

new Thread(() -> object1.criticalMethod()).start(); // locks object1
new Thread(() -> object2.criticalMethod()).start(); // locks object2 — runs in parallel!
```

**Expected:** one thread waits.  
**Actual:** both threads enter simultaneously — because they hold different monitors.

---

## How Monitor Locks Work

Every Java object has a built-in **monitor**. `synchronized void method()` is syntactic sugar for `synchronized(this)`.

Properties of intrinsic locks:

| Property | Behaviour |
|---|---|
| Acquire/release | Automatic |
| Reentrant | Yes — same thread can re-enter |
| Interruptible | No — waits indefinitely |
| Timeout | No |
| Fairness control | No |

---

## Three Solutions to the Multi-Object Problem

### Solution 1 — Share the Same Instance

```java
CriticalClass shared = new CriticalClass();
new Thread(() -> shared.criticalMethod()).start();
new Thread(() -> shared.criticalMethod()).start();
// Both threads compete for the same monitor — sequential access
```

### Solution 2 — Class-Level Lock (static synchronized)

```java
class CriticalClass {
    static synchronized void criticalMethod() {
        System.out.println(Thread.currentThread().getName() + " entered");
        try { Thread.sleep(2000); } catch (InterruptedException ignored) {}
        System.out.println(Thread.currentThread().getName() + " leaving");
    }
}
// Locks CriticalClass.class — enforced across ALL instances
```

### Solution 3 — ReentrantLock (Explicit Control)

```java
import java.util.concurrent.locks.ReentrantLock;

class CriticalClass {
    private static final ReentrantLock lock = new ReentrantLock();

    void criticalMethod() {
        lock.lock();
        try {
            System.out.println(Thread.currentThread().getName() + " entered");
            Thread.sleep(2000);
            System.out.println(Thread.currentThread().getName() + " leaving");
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        } finally {
            lock.unlock(); // ALWAYS in finally
        }
    }
}
```

The lock is `static` — shared across all instances regardless of how many objects are created.

---

## synchronized vs ReentrantLock

| Aspect | `synchronized` | `ReentrantLock` |
|---|---|---|
| Control | Automatic | Manual |
| Interruptible | No | Yes (`lockInterruptibly()`) |
| Timeout | No | Yes (`tryLock(long, TimeUnit)`) |
| Fairness | No | Yes (`new ReentrantLock(true)`) |
| Multiple conditions | One (wait/notify) | Many (`newCondition()`) |
| Try-acquire | No | Yes (`tryLock()`) |
| Flexibility | Low | High |

**The golden rule:** the lock protects **data**, not methods. All threads must use the **same lock instance** to establish mutual exclusion.

---

## ReentrantLock — Full API

```java
lock()                          // Acquire lock (blocks indefinitely)
lockInterruptibly()             // Acquire lock, throw InterruptedException if interrupted
tryLock()                       // Try to acquire, return boolean immediately
tryLock(long time, TimeUnit u)  // Try to acquire within timeout
unlock()                        // Release lock — always call in finally

newCondition()                  // Create a Condition variable for this lock
getHoldCount()                  // How many times current thread holds this lock
isLocked()                      // Whether any thread holds the lock
isFair()                        // Whether this lock uses fair ordering
isHeldByCurrentThread()         // Whether the current thread holds this lock
```

---

## ReentrantLock with Fairness — Full Example

Fair lock grants access in the order threads requested it, preventing starvation.

```java
import java.util.concurrent.locks.ReentrantLock;

public class FairLockExample {
    public static void main(String[] args) {
        SharedResource resource = new SharedResource();

        for (int i = 0; i < 5; i++) {
            new Thread(resource::accessResource, "Thread-" + i).start();
        }
    }
}

class SharedResource {
    private final ReentrantLock lock = new ReentrantLock(true); // fair

    public void accessResource() {
        lock.lock();
        try {
            System.out.println(Thread.currentThread().getName() + " acquired lock");
            Thread.sleep(2000);
            System.out.println(Thread.currentThread().getName() + " releasing lock");
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        } finally {
            lock.unlock();
        }
    }
}
```

---

## Condition Variables

`Condition` replaces `Object.wait()` / `notify()` when using `ReentrantLock`. A single lock can have **multiple** conditions, allowing fine-grained thread signalling.

```java
Lock lock = new ReentrantLock();
Condition condition = lock.newCondition();

condition.await();   // releases lock and suspends — equivalent to Object.wait()
condition.signal();  // wakes one waiting thread — equivalent to Object.notify()
condition.signalAll(); // wakes all waiting threads
```

### How the Flow Works

```java
private final Lock lock = new ReentrantLock();
private final Condition conditionMet = lock.newCondition();

// Thread-1: waits for a condition
public void method1() throws InterruptedException {
    lock.lock();
    try {
        System.out.println("Thread-1: waiting for condition...");
        conditionMet.await();  // releases lock, suspends here
        // resumes here after signal — lock re-acquired automatically
        System.out.println("Thread-1: condition met, proceeding");
    } finally {
        lock.unlock();
    }
}

// Thread-2: signals the condition
public void method2() {
    lock.lock();
    try {
        System.out.println("Thread-2: performing work...");
        Thread.sleep(2000);
        System.out.println("Thread-2: signalling condition");
        conditionMet.signal(); // wakes Thread-1
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    } finally {
        lock.unlock();
    }
}
```

**Flow:** Thread-1 acquires lock → `await()` releases lock and suspends → Thread-2 acquires lock → does work → `signal()` wakes Thread-1 → Thread-1 re-acquires lock and resumes.

---

## Spurious Wakeups

A **spurious wakeup** is when a thread returns from `await()` / `wait()` without being signalled. This is permitted behaviour in Java (and POSIX). Causes include OS-level interrupts and JVM scheduling decisions — it is not a bug.

**Wrong — using `if`:**

```java
// BAD: if spurious wakeup occurs, count == 0 is not re-checked
if (count == 0)
    condition.await();
return getData(); // may run even though buffer is still empty
```

**Correct — always use `while`:**

```java
// GOOD: re-checks condition after every wakeup
while (count == 0)
    condition.await();
return getData();
```

### Interview Questions on Spurious Wakeups

**Q: Why use `while` instead of `if` with condition variables?**
Spurious wakeups can return from `await()` without a signal. A `while` loop re-validates the predicate after every wakeup.

**Q: Is a spurious wakeup a bug?**
No. It is documented behaviour in Java and POSIX. Preventing it entirely would be more expensive than defending against it with a loop.

**Q: Can spurious wakeups cause data corruption?**
No, if you use the `while` pattern correctly. The lock ensures only one thread modifies data at a time.

---

## Producer-Consumer with Condition Variables — Full Example

```java
import java.util.*;
import java.util.concurrent.locks.*;

public class ProducerConsumerExample {
    public static void main(String[] args) {
        Buffer buffer = new Buffer(5);

        for (int i = 0; i < 2; i++) {
            new Thread(() -> {
                try {
                    for (int j = 0; j < 10; j++) {
                        buffer.produce("Item-" + j);
                        Thread.sleep(100);
                    }
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }, "Producer-" + i).start();
        }

        for (int i = 0; i < 2; i++) {
            new Thread(() -> {
                try {
                    for (int j = 0; j < 10; j++) {
                        String item = buffer.consume();
                        System.out.println(Thread.currentThread().getName() + " consumed: " + item);
                        Thread.sleep(150);
                    }
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }, "Consumer-" + i).start();
        }
    }
}

class Buffer {
    private final Queue<String> queue = new LinkedList<>();
    private final int capacity;
    private final Lock lock = new ReentrantLock();
    private final Condition notEmpty = lock.newCondition();
    private final Condition notFull  = lock.newCondition();

    public Buffer(int capacity) {
        this.capacity = capacity;
    }

    public void produce(String item) throws InterruptedException {
        lock.lock();
        try {
            while (queue.size() == capacity) {           // while — not if
                System.out.println(Thread.currentThread().getName() + " waiting (full)");
                notFull.await();
            }
            queue.add(item);
            System.out.println(Thread.currentThread().getName() + " produced: " + item);
            notEmpty.signal();
        } finally {
            lock.unlock();
        }
    }

    public String consume() throws InterruptedException {
        lock.lock();
        try {
            while (queue.isEmpty()) {                    // while — not if
                System.out.println(Thread.currentThread().getName() + " waiting (empty)");
                notEmpty.await();
            }
            String item = queue.poll();
            notFull.signal();
            return item;
        } finally {
            lock.unlock();
        }
    }
}
```

---

## Common Exceptions

| Exception | Cause | Fix |
|---|---|---|
| `InterruptedException` | Thread interrupted during `lock.lockInterruptibly()` or `await()` | Restore interrupt flag; exit gracefully |
| `IllegalMonitorStateException` | `unlock()` called without holding the lock | Always acquire before releasing; use try-finally |
| `IllegalArgumentException` | Negative timeout in `tryLock` | Validate arguments |
