# Java Locks — Part 2: ReadWriteLock, StampedLock, Semaphore


## Table of Contents

- [1. ReadWriteLock](#1-readwritelock)
  - [1.1 Rules](#11-rules)
  - [1.2 Lock Types Comparison](#12-lock-types-comparison)
  - [1.3 API](#13-api)
  - [1.4 Full Example — Cache](#14-full-example--cache)
- [2. StampedLock](#2-stampedlock)
  - [2.1 Three Modes](#21-three-modes)
  - [2.2 API](#22-api)
  - [2.3 Optimistic Read Pattern](#23-optimistic-read-pattern)
  - [2.4 Full Example — Point](#24-full-example--point)
  - [2.5 Caveats](#25-caveats)
- [3. Semaphore](#3-semaphore)
  - [3.1 Basics](#31-basics)
  - [3.2 API](#32-api)
  - [3.3 Full Example — Connection Pool](#33-full-example--connection-pool)
- [4. Lock Comparison](#4-lock-comparison)
  - [4.1 Comparison Table](#41-comparison-table)
  - [4.2 When to Use Which](#42-when-to-use-which)
- [5. Best Practices](#5-best-practices)

---

## 1. ReadWriteLock

Splits locking into two separate locks — a shared read lock and an exclusive write lock — maximising throughput for read-heavy workloads.

### 1.1 Rules

- Multiple threads can hold the read lock simultaneously, as long as no writer holds the write lock.
- Only one thread can hold the write lock. While held, no readers are allowed.

```java
ReadWriteLock rwLock = new ReentrantReadWriteLock();

// Read operation — shared
rwLock.readLock().lock();
try {
    // read data
} finally {
    rwLock.readLock().unlock();
}

// Write operation — exclusive
rwLock.writeLock().lock();
try {
    // write data
} finally {
    rwLock.writeLock().unlock();
}
```

### 1.2 Lock Types Comparison

| Lock Type | Multiple Threads? | Allows Write? | Used Via |
|---|---|---|---|
| Shared (read) | Yes | No | `ReadWriteLock.readLock()` |
| Exclusive (write) | No | Yes | `ReadWriteLock.writeLock()` |
| Optimistic read | Yes (no lock taken) | Yes, if stamp still valid | `StampedLock.tryOptimisticRead()` |

> **Note:** `ReadWriteLock` / `ReentrantReadWriteLock` does not support optimistic read. That feature belongs to `StampedLock`.

### 1.3 API

```
readLock()    // returns the shared read lock
writeLock()   // returns the exclusive write lock
```

Both returned locks support the full `Lock` interface: `lock()`, `unlock()`, `tryLock()`, `tryLock(timeout, unit)`, `lockInterruptibly()`.

### 1.4 Full Example — Cache

```java
import java.util.*;
import java.util.concurrent.locks.*;

public class CacheExample {
    public static void main(String[] args) {
        Cache cache = new Cache();

        // Reader threads
        for (int i = 0; i < 3; i++) {
            new Thread(() -> {
                for (int j = 0; j < 5; j++) {
                    System.out.println("Read: " + cache.get("key" + j));
                    try { Thread.sleep(100); } catch (InterruptedException e) { Thread.currentThread().interrupt(); }
                }
            }, "Reader-" + i).start();
        }

        // Writer thread
        new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                cache.put("key" + i, "value" + i);
                try { Thread.sleep(500); } catch (InterruptedException e) { Thread.currentThread().interrupt(); }
            }
        }, "Writer").start();
    }
}

class Cache {
    private final Map<String, String> map = new HashMap<>();
    private final ReadWriteLock lock = new ReentrantReadWriteLock();

    public String get(String key) {
        lock.readLock().lock();
        try {
            System.out.println(Thread.currentThread().getName() + " reading: " + key);
            Thread.sleep(100);
            return map.get(key);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
            return null;
        } finally {
            lock.readLock().unlock();
        }
    }

    public void put(String key, String value) {
        lock.writeLock().lock();
        try {
            System.out.println(Thread.currentThread().getName() + " writing: " + key);
            map.put(key, value);
            Thread.sleep(200);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        } finally {
            lock.writeLock().unlock();
        }
    }
}
```

---

## 2. StampedLock

Available since **Java 8**. Extends the read/write model with a third mode — **optimistic read** — that avoids acquiring any lock at all. Every operation returns a numeric **stamp** used to release or validate the lock.

### 2.1 Three Modes

| Mode | Method | Blocks? | Notes |
|---|---|---|---|
| Write | `writeLock()` | Yes | Exclusive; all others blocked |
| Read | `readLock()` | Yes | Shared; blocks writers |
| Optimistic read | `tryOptimisticRead()` | No | Returns stamp; validate before using data |

### 2.2 API

```java
long writeLock()              // acquire write lock, returns stamp
long readLock()               // acquire read lock, returns stamp
long tryOptimisticRead()      // non-blocking, returns stamp (0 if write lock held)
boolean validate(long stamp)  // true if no write lock acquired since stamp was issued
unlockWrite(long stamp)       // release write lock
unlockRead(long stamp)        // release read lock
```

### 2.3 Optimistic Read Pattern

```java
StampedLock lock = new StampedLock();

long stamp = lock.tryOptimisticRead(); // no lock acquired
double x = this.x;
double y = this.y;

if (!lock.validate(stamp)) {           // a writer intervened — fall back
    stamp = lock.readLock();
    try {
        x = this.x;
        y = this.y;
    } finally {
        lock.unlockRead(stamp);
    }
}
// use x, y safely
```

> **Why it is faster:** Under low write contention, optimistic reads never block. The validation check is a single compare.

### 2.4 Full Example — Point

```java
import java.util.concurrent.locks.StampedLock;

public class OptimisticReadExample {
    public static void main(String[] args) {
        Point point = new Point(10, 20);

        // Reader with optimistic reads
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                System.out.println("Distance: " + point.distanceFromOrigin());
                try { Thread.sleep(100); } catch (InterruptedException e) { Thread.currentThread().interrupt(); }
            }
        }, "Reader").start();

        // Writer
        new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                point.move(i, i);
                try { Thread.sleep(300); } catch (InterruptedException e) { Thread.currentThread().interrupt(); }
            }
        }, "Writer").start();
    }
}

class Point {
    private double x, y;
    private final StampedLock lock = new StampedLock();

    public Point(double x, double y) { this.x = x; this.y = y; }

    public double distanceFromOrigin() {
        long stamp = lock.tryOptimisticRead();
        double curX = x, curY = y;

        if (!lock.validate(stamp)) {       // writer intervened
            stamp = lock.readLock();
            try {
                curX = x;
                curY = y;
            } finally {
                lock.unlockRead(stamp);
            }
        }
        return Math.sqrt(curX * curX + curY * curY);
    }

    public void move(double deltaX, double deltaY) {
        long stamp = lock.writeLock();
        try {
            x += deltaX;
            y += deltaY;
            System.out.println("Point moved to (" + x + ", " + y + ")");
        } finally {
            lock.unlockWrite(stamp);
        }
    }
}
```

### 2.5 Caveats

| Caveat | Detail |
|---|---|
| Not reentrant | Calling `readLock()` from a thread already holding the write lock will deadlock |
| No Condition support | Cannot use `await()` / `signal()` with StampedLock |
| Do not use when | Reentrancy or Conditions are required — use `ReentrantReadWriteLock` instead |

---

## 3. Semaphore

A **Semaphore** maintains a set of permits. `acquire()` takes a permit (blocking if none available). `release()` returns a permit. Used to **limit concurrent access** to a resource, not to establish mutual exclusion between threads competing for the same data.

### 3.1 Basics

```java
Semaphore semaphore = new Semaphore(3); // 3 concurrent permits

semaphore.acquire();   // blocks until permit available
try {
    // use resource
} finally {
    semaphore.release();
}
```

### 3.2 API

```java
new Semaphore(int permits)                    // unfair
new Semaphore(int permits, boolean fair)      // fair ordering

acquire()                                     // acquire 1 permit, blocking
acquire(int permits)                          // acquire N permits, blocking
tryAcquire()                                  // non-blocking, returns boolean
tryAcquire(long timeout, TimeUnit u)          // timed attempt
release()                                     // release 1 permit
release(int permits)                          // release N permits
availablePermits()                            // current permit count
```

### 3.3 Full Example — Connection Pool

```java
import java.util.concurrent.Semaphore;

public class ConnectionPoolExample {
    public static void main(String[] args) {
        ConnectionPool pool = new ConnectionPool(3);

        for (int i = 0; i < 10; i++) {
            new Thread(() -> {
                try {
                    Connection conn = pool.getConnection();
                    Thread.sleep(2000); // use connection
                    pool.releaseConnection(conn);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }, "Client-" + i).start();
        }
    }
}

class ConnectionPool {
    private final Semaphore available;
    private final Connection[] connections;
    private final boolean[] used;

    public ConnectionPool(int size) {
        available   = new Semaphore(size, true); // fair
        connections = new Connection[size];
        used        = new boolean[size];
        for (int i = 0; i < size; i++) {
            connections[i] = new Connection("Connection-" + i);
        }
    }

    public Connection getConnection() throws InterruptedException {
        available.acquire();
        return nextAvailable();
    }

    public void releaseConnection(Connection connection) {
        if (markUnused(connection)) available.release();
    }

    private synchronized Connection nextAvailable() {
        for (int i = 0; i < used.length; i++) {
            if (!used[i]) {
                used[i] = true;
                System.out.println(Thread.currentThread().getName() + " acquired " + connections[i].getName());
                return connections[i];
            }
        }
        return null; // never reached if semaphore permits match pool size
    }

    private synchronized boolean markUnused(Connection conn) {
        for (int i = 0; i < connections.length; i++) {
            if (connections[i] == conn && used[i]) {
                used[i] = false;
                System.out.println(Thread.currentThread().getName() + " released " + conn.getName());
                return true;
            }
        }
        return false;
    }
}

class Connection {
    private final String name;
    Connection(String name) { this.name = name; }
    String getName() { return name; }
}
```

---

## 4. Lock Comparison

### 4.1 Comparison Table

| Lock | Fairness | Interruptible | Timeout | Conditions | Performance |
|---|---|---|---|---|---|
| `synchronized` | No | No | No | One (`wait`/`notify`) | Good |
| `ReentrantLock` | Optional | Yes | Yes | Multiple | Good |
| `ReadWriteLock` | Optional | Yes | Yes | Multiple | Better for reads |
| `StampedLock` | No | Yes | Yes | None | Best (optimistic) |
| `Semaphore` | Optional | Yes | Yes | None | Good |

### 4.2 When to Use Which

| Scenario | Choice | Reason |
|---|---|---|
| Simple object-level thread safety | `synchronized` | Built-in, zero boilerplate |
| Interruptibility or timeout needed | `ReentrantLock` | Full control over locking |
| Many reads, few writes | `ReadWriteLock` | Shared read lock improves throughput |
| Highest read performance, low write contention | `StampedLock` | Optimistic reads avoid all locking |
| Limit concurrent access to a pool or rate-limit | `Semaphore` | Permit-based access control |
| Thread communication without a monitor | `Condition` | Signal/await on explicit lock |

---

## 5. Best Practices

### 5.1 Always Unlock in a Finally Block

Without a `finally` block, an exception leaves the lock permanently held.

```java
lock.lock();
try {
    // critical section
} finally {
    lock.unlock(); // executes even if exception is thrown
}
```

### 5.2 Never Mix synchronized and Explicit Locks

> **Warning:** Never mix `synchronized` and explicit `Lock` on the same shared data. They are independent mechanisms and will not coordinate with each other.