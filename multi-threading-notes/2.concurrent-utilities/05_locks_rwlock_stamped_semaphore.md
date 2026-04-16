# Java Locks — Part 2: ReadWriteLock, StampedLock, Semaphore

---

## ReadWriteLock

Splits locking into two separate locks — a **shared read lock** and an **exclusive write lock** — maximising throughput for read-heavy workloads.

**Rules:**
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

### Shared vs Exclusive vs Optimistic — Comparison

| Lock Type | Multiple Threads? | Allows Write? | Used Via |
|---|---|---|---|
| Shared (read) | Yes | No | `ReadWriteLock.readLock()` |
| Exclusive (write) | No | Yes | `ReadWriteLock.writeLock()` |
| Optimistic read | Yes (no lock taken) | Yes, if stamp still valid | `StampedLock.tryOptimisticRead()` |

### ReadWriteLock API

```java
readLock()    // returns the shared read lock
writeLock()   // returns the exclusive write lock
```

Both returned locks support the full `Lock` interface: `lock()`, `unlock()`, `tryLock()`, `tryLock(timeout, unit)`, `lockInterruptibly()`.

### ReadWriteLock Cache — Full Example

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
    private final Map<String, String> map  = new HashMap<>();
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

## StampedLock (Java 8+)

`StampedLock` extends the read/write model with a third mode — **optimistic read** — that avoids acquiring any lock at all. Every operation returns a numeric **stamp** used to release or validate the lock.

### Three Modes

| Mode | Method | Blocks? | Notes |
|---|---|---|---|
| Write | `writeLock()` | Yes | Exclusive; all others blocked |
| Read | `readLock()` | Yes | Shared; blocks writers |
| Optimistic read | `tryOptimisticRead()` | No | Returns stamp; validate before using data |

### StampedLock API

```java
long writeLock()              // acquire write lock, returns stamp
long readLock()               // acquire read lock, returns stamp
long tryOptimisticRead()      // non-blocking, returns stamp (0 if write lock held)
boolean validate(long stamp)  // true if no write lock acquired since stamp was issued
unlockWrite(long stamp)       // release write lock
unlockRead(long stamp)        // release read lock
```

### Optimistic Read Pattern

```java
StampedLock lock = new StampedLock();

long stamp = lock.tryOptimisticRead(); // no lock acquired
double x = this.x;
double y = this.y;

if (!lock.validate(stamp)) {          // a writer intervened — fall back
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

**Why it is faster:** under low write contention, optimistic reads never block. The validation check is a single compare.

### StampedLock — Full Example (Point)

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

### StampedLock Caveats

- `StampedLock` is **not reentrant**. Calling `readLock()` from a thread already holding a write lock will deadlock.
- There is no `Condition` support.
- Do not use `StampedLock` where `ReentrantReadWriteLock` features (reentrancy, conditions) are required.

---

## Semaphore

A `Semaphore` maintains a set of **permits**. `acquire()` takes a permit (blocking if none available). `release()` returns a permit. Used to limit concurrent access to a resource — not to establish mutual exclusion between threads competing for the same data.

```java
Semaphore semaphore = new Semaphore(3); // 3 concurrent permits

semaphore.acquire();   // blocks until permit available
try {
    // use resource
} finally {
    semaphore.release();
}
```

### Semaphore API

```java
new Semaphore(int permits)            // unfair
new Semaphore(int permits, boolean fair)

acquire()                             // acquire 1 permit, blocking
acquire(int permits)                  // acquire N permits, blocking
tryAcquire()                          // non-blocking, returns boolean
tryAcquire(long timeout, TimeUnit u)  // timed attempt
release()                             // release 1 permit
release(int permits)                  // release N permits
availablePermits()                    // current permit count
```

### Semaphore Connection Pool — Full Example

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
        available    = new Semaphore(size, true); // fair
        connections  = new Connection[size];
        used         = new boolean[size];
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

## Lock Comparison Table

| Lock | Fairness | Interruptible | Timeout | Conditions | Performance |
|---|---|---|---|---|---|
| `synchronized` | No | No | No | One (wait/notify) | Good |
| `ReentrantLock` | Optional | Yes | Yes | Multiple | Good |
| `ReadWriteLock` | Optional | Yes | Yes | Multiple | Better for reads |
| `StampedLock` | No | Yes | Yes | None | Best (optimistic) |
| `Semaphore` | Optional | Yes | Yes | None | Good |

---

## When to Use Which

| Scenario | Choice | Reason |
|---|---|---|
| Simple object-level thread safety | `synchronized` | Built-in, zero boilerplate |
| Interruptibility or timeout needed | `ReentrantLock` | Full control |
| Many reads, few writes | `ReadWriteLock` | Shared read lock improves throughput |
| Highest read performance, low write contention | `StampedLock` | Optimistic reads avoid all locking |
| Limit concurrent access to a pool / rate-limit | `Semaphore` | Permit-based access control |
| Thread communication without a monitor | `Condition` | Signal/await on explicit lock |

---

## Best Practices

Always unlock in a `finally` block — without it, an exception leaves the lock permanently held:

```java
lock.lock();
try {
    // critical section
} finally {
    lock.unlock(); // executes even if exception thrown
}
```

Never mix `synchronized` and explicit `Lock` on the same shared data — they are independent mechanisms and will not coordinate with each other.
