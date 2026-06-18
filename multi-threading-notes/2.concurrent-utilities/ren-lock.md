### Why do we need `ReentrantLock`?

`synchronized` already provides basic locking, but `ReentrantLock` gives **more control and additional features**.

---

## Problem 1: Need to try acquiring a lock without waiting

### Using `synchronized`

```java
synchronized(lock) {
    // thread waits indefinitely if lock is busy
}
```

If another thread holds the lock, your thread gets blocked.

### Using `ReentrantLock`

```java
Lock lock = new ReentrantLock();

if(lock.tryLock()) {
    try {
        // critical section
    } finally {
        lock.unlock();
    }
} else {
    System.out.println("Lock busy, doing something else");
}
```

**Use case:** Payment service, cache update, scheduled jobs where waiting is undesirable.

---

## Problem 2: Need timeout while waiting

With `synchronized`, you cannot say:

> "Wait only 5 seconds for the lock."

### ReentrantLock

```java
if(lock.tryLock(5, TimeUnit.SECONDS)) {
    try {
        // work
    } finally {
        lock.unlock();
    }
}
```

If lock isn't available within 5 seconds, the thread can take another action.

---

## Problem 3: Need interruptible lock waiting

### `synchronized`

A waiting thread cannot easily be interrupted.

### `ReentrantLock`

```java
lock.lockInterruptibly();
```

If another thread interrupts it:

```java
thread.interrupt();
```

it immediately stops waiting and throws `InterruptedException`.

**Use case:** Long-running servers, thread pools.

---

## Problem 4: Fairness (FIFO ordering)

### `synchronized`

No fairness guarantee.

Thread A waiting first may still lose to Thread B.

### `ReentrantLock`

```java
Lock lock = new ReentrantLock(true);
```

`true` = Fair Lock

Threads get the lock roughly in arrival order.

---

## Problem 5: Multiple Condition Variables

Every Java object has only one monitor wait set.

With `synchronized`:

```java
wait();
notify();
notifyAll();
```

All waiting threads share the same queue.

### ReentrantLock

```java
Lock lock = new ReentrantLock();

Condition notEmpty = lock.newCondition();
Condition notFull = lock.newCondition();
```

Different waiting queues for different conditions.

Example:

```java
notEmpty.await();
notEmpty.signal();

notFull.await();
notFull.signal();
```

Used heavily in producer-consumer systems.

---

## What does "Reentrant" mean?

A thread that already owns the lock can acquire it again.

```java
Lock lock = new ReentrantLock();

public void methodA() {
    lock.lock();
    try {
        methodB();
    } finally {
        lock.unlock();
    }
}

public void methodB() {
    lock.lock();   // same thread can acquire again
    try {
        // work
    } finally {
        lock.unlock();
    }
}
```

Without reentrancy, this would deadlock.

The lock maintains a **hold count**:

```java
lock.lock();  // count = 1
lock.lock();  // count = 2

lock.unlock(); // count = 1
lock.unlock(); // count = 0 (released)
```

---

## Interview Answer (Short)

> `ReentrantLock` is an advanced locking mechanism in Java that provides features not available with `synchronized`, such as `tryLock()`, timed lock acquisition, interruptible locking, fairness policies, and multiple condition variables. It is called reentrant because a thread holding the lock can acquire it multiple times without causing deadlock. It is commonly used when finer control over thread synchronization is required.

---

## `synchronized` vs `ReentrantLock`

| Feature                | synchronized | ReentrantLock            |
| ---------------------- | ------------ | ------------------------ |
| Basic mutual exclusion | ✅            | ✅                        |
| Reentrant              | ✅            | ✅                        |
| tryLock()              | ❌            | ✅                        |
| Timeout support        | ❌            | ✅                        |
| Interruptible waiting  | ❌            | ✅                        |
| Fair lock option       | ❌            | ✅                        |
| Multiple Conditions    | ❌            | ✅                        |
| Automatic release      | ✅            | ❌ (must unlock manually) |

### Rule of Thumb

* Use **`synchronized`** for simple locking.
* Use **`ReentrantLock`** when you need:

  * `tryLock()`
  * timeout support
  * interruptible waits
  * fairness
  * multiple condition queues.
