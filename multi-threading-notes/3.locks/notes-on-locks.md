# 🔒 Comprehensive Java Locks Guide - Theory & Code

## 📋 Table of Contents
1. [Basic Locks](#basic-locks)
2. [Advanced Locks](#advanced-locks)
3. [Synchronization Utilities](#synchronization-utilities)
4. [Lock Comparison Table](#lock-comparison-table)
5. [When to Use Which Lock](#when-to-use-which-lock)
6. [Complete Code Examples](#complete-code-examples)


## Understanding Java Locks

### The Issue with `synchronized`
Using `synchronized` locks the specific object instance, not globally. This can lead to multiple threads modifying shared data simultaneously, causing concurrency issues.

### Example of the Problem
```java
class CriticalClass {
    synchronized void criticalMethod() {
        System.out.println(Thread.currentThread().getName() + " entered");
        try { Thread.sleep(2000); } catch (Exception ignored) {}
        System.out.println(Thread.currentThread().getName() + " leaving");
    }
}

CriticalClass object1 = new CriticalClass();
CriticalClass object2 = new CriticalClass();

Thread thread1 = new Thread(() -> object1.criticalMethod());
Thread thread2 = new Thread(() -> object2.criticalMethod());

thread1.start();
thread2.start();
```
**Expected Output:** One thread waits, then enters.  
**Actual Output:** Both threads enter simultaneously.

### Key Insight
- **`synchronized` locks the object, not the method globally.**

### How Monitor Locks Work
Every Java object owns a **monitor** (a lock). `synchronized` translates to `synchronized(this)`, locking the specific object instance.

**Properties:**
- ✅ Automatic acquire/release
- ✅ Simple syntax
- ✅ Reentrant (same thread can re-enter)
- ❌ NOT interruptible while waiting
- ❌ No timeout
- ❌ No fairness control

### Solution 1: Share the Same Object
```java
CriticalClass shared = new CriticalClass();

Thread t1 = new Thread(() -> shared.criticalMethod());
Thread t2 = new Thread(() -> shared.criticalMethod());

t1.start();
t2.start();
```
**Output:** Both threads access the method sequentially.

### Solution 2: Class-Level Lock (static synchronized)
```java
class CriticalClass {
    static synchronized void criticalMethod() {
        System.out.println(Thread.currentThread().getName() + " entered");
        try { Thread.sleep(2000); } catch (Exception ignored) {}
        System.out.println(Thread.currentThread().getName() + " leaving");
    }
}
```
**Result:** Only one thread enters, even with different objects.

### Solution 3: ReentrantLock (Explicit Control)
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
        } catch (InterruptedException ignored) {
        } finally {
            lock.unlock();
        }
    }
}
```
**Why choose this?** ReentrantLock handles interruptibility, timeouts, and fairness.

### Comparison: synchronized vs ReentrantLock
| Aspect | synchronized | ReentrantLock |
|--------|--------------|---------------|
| **Control** | Automatic | Manual |
| **Flexibility** | Low | High |
| **Interruptible** | ❌ No | ✅ Yes |
| **Timeout** | ❌ No | ✅ Yes |
| **Fairness** | ❌ No | ✅ Yes |

### The Golden Rule
```
Lock protects DATA, not METHODS.
```
Always ensure all threads use the SAME lock to prevent race conditions.


---
<br/><br/>

## 🔐 Basic Locks

### 1. **Intrinsic Lock (synchronized keyword)**
- Implicit lock per object.
- One thread can acquire per object at a time.
- If multiple objects are used, threads may bypass the lock.
- **Built-in** lock mechanism in Java
- **Implicit** acquisition and release
- **Object-level** or **class-level** locking
- **Non-interruptible** - thread waits indefinitely
```java
synchronized void method() {
    // critical section
}

synchronized(this) {
    // critical section
}
```


### 2. **ReentrantLock**
- **Explicit** lock with manual control (not bound to object).
- **Reentrant** - same thread can acquire multiple times, Works across multiple objects as long as same lock object is shared.
- **Fair/Unfair** scheduling options
- **Interruptible** - can be interrupted while waiting
- Must call `lock()` and `unlock()` manually.

```java
ReentrantLock lock = new ReentrantLock(true); // fair lock
lock.lock();
try {
    // critical section
} finally {
    lock.unlock();
}
```

### 3. **ReadWriteLock**
- **Separate** read and write locks. Separates **Read (Shared Lock)** and **Write (Exclusive Lock)**.
- **Multiple readers** can access simultaneously. ( Multiple readers allowed if no writer.Only one writer allowed, no reader during write. )
- **Exclusive writer** access
- **Improved performance** for read-heavy workloads

```java
ReadWriteLock rwLock = new ReentrantReadWriteLock();
rwLock.readLock().lock();   // shared lock
rwLock.writeLock().lock();  // exclusive lock
```
### 🔁 Shared vs Exclusive Lock – Table

| Lock Type       | Allows Multiple Threads? | Allows Write? | Used In                           |
| --------------- | ------------------------ | ------------- | --------------------------------- |
| Shared Lock     | ✅ Yes (multiple reads)   | ❌ No          | ReadWriteLock - readLock()        |
| Exclusive Lock  | ❌ No (only 1 thread)     | ✅ Yes         | ReadWriteLock - writeLock()       |
| Optimistic Lock | ✅ Yes (without locking)  | ✅ If valid    | StampedLock - tryOptimisticRead() |


---

## 🚀 Advanced Locks

### 4. **StampedLock (Java 8+)**
- **Three modes**: Read, Write, Optimistic Read
- **Non-blocking** optimistic reads
- **Better performance** than ReadWriteLock
- **Stamp-based** validation mechanism

```java
StampedLock lock = new StampedLock();
long stamp = lock.tryOptimisticRead();
// read data
if (!lock.validate(stamp)) {
    // fallback to read lock
}
```

### 5. **Semaphore**
- **Resource counting** mechanism
- **Controls access** to limited resources
- **Permits-based** system
- **Useful for** connection pools, rate limiting

```java
Semaphore semaphore = new Semaphore(3); // 3 permits
semaphore.acquire();
try {
    // use resource
} finally {
    semaphore.release();
}
```

### 6. **Condition Variables**
- **Alternative** to wait/notify
- **Multiple conditions** per lock
- **More flexible** thread communication
- **Works with** ReentrantLock
#### How Condition Variables Work

####  Condition variables enable thread communication through explicit signal/await mechanism:

```java
private Lock lock = new ReentrantLock();
private Condition conditionMet = lock.newCondition();

// Thread-1: Waits for condition
public void method1() throws InterruptedException {
    lock.lock();
    try {
        System.out.println("Thread-1: Waiting for condition...");
        conditionMet.await();  // Suspend here - releases lock, waits for signal
        // Resume here - lock re-acquired after signal
        System.out.println("Thread-1: Condition met, proceeding with dependent operations");
    } finally {
        lock.unlock();
    }
}

// Thread-2: Signals the condition
public void method2() {
    lock.lock();
    try {
        System.out.println("Thread-2: Performing operations...");
        Thread.sleep(2000);
        System.out.println("Thread-2: Signaling condition");
        conditionMet.signal();  // Wake up one waiting thread
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    } finally {
        lock.unlock();
    }
}

// Example usage
public static void main(String[] args) {
    Example example = new Example();
    
    Thread t1 = new Thread(() -> {
        try {
            example.method1();
        } catch (InterruptedException e) {}
    }, "Thread-1");
    
    Thread t2 = new Thread(() -> example.method2(), "Thread-2");
    
    t1.start();
    t2.start();
}
```

**Key Flow:**
- Thread-1 acquires lock → calls `await()` → **releases lock and suspends**
- Thread-2 acquires lock → performs work → calls `signal()` → **wakes Thread-1**
- Thread-1 **re-acquires lock** → resumes execution
```java
Lock lock = new ReentrantLock();
Condition condition = lock.newCondition();
condition.await();  // like wait()
condition.signal(); // like notify()
```
### 7. **Spurious Wakeups & Producer-Consumer Pattern**
- **Unexpected thread wake** without explicit signal
- **Can occur** in wait/await calls
- **Always use loops** with condition checks
- **Best practice**: while-loop instead of if-statement

#### What is a Spurious Wakeup?

A spurious wakeup occurs when a thread wakes up from `wait()` or `await()` **without being explicitly signaled**. This is a legitimate behavior in Java (and POSIX systems) where threads can wake up unpredictably due to OS-level interrupts or other system events.

#### Why Does It Happen?

- **OS scheduling**: System interrupts can wake threads
- **JVM implementation**: Platform-specific behavior
- **Not a bug**: It's a documented feature, not an error
- **Performance trade-off**: Cheaper than guaranteeing no spurious wakeups

#### The Problem
```java
// ❌ WRONG - Using if statement
public String consume() throws InterruptedException {
    lock.lock();
    try {
        if (count == 0)  // Dangerous!
            added.await();
        return getData();
    } finally {
        lock.unlock();
    }
}
```
If spurious wakeup occurs, condition `count == 0` is no longer checked!

#### The Solution
```java
// ✅ CORRECT - Using while loop
public String consume() throws InterruptedException {
    lock.lock();
    try {
        while (count == 0)  // Re-check after wakeup
            added.await();
        return getData();
    } finally {
        lock.unlock();
    }
}
```

#### Complete Producer-Consumer Example
```java
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;
import java.util.LinkedList;
import java.util.Queue;

public class ProducerConsumerExample {
    public static void main(String[] args) {
        Buffer buffer = new Buffer(5);
        
        // Producer threads
        for (int i = 0; i < 2; i++) {
            Thread producer = new Thread(() -> {
                try {
                    for (int j = 0; j < 10; j++) {
                        buffer.produce("Item-" + j);
                        Thread.sleep(100);
                    }
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }, "Producer-" + i);
            producer.start();
        }
        
        // Consumer threads
        for (int i = 0; i < 2; i++) {
            Thread consumer = new Thread(() -> {
                try {
                    for (int j = 0; j < 10; j++) {
                        String item = buffer.consume();
                        System.out.println(Thread.currentThread().getName() + " consumed: " + item);
                        Thread.sleep(150);
                    }
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }, "Consumer-" + i);
            consumer.start();
        }
    }
}

class Buffer {
    private final Queue<String> queue;
    private final int capacity;
    private final Lock lock = new ReentrantLock();
    private final Condition notEmpty = lock.newCondition();
    private final Condition notFull = lock.newCondition();
    
    public Buffer(int capacity) {
        this.capacity = capacity;
        this.queue = new LinkedList<>();
    }
    
    public void produce(String item) throws InterruptedException {
        lock.lock();
        try {
            // while loop handles spurious wakeups
            while (queue.size() == capacity) {
                System.out.println(Thread.currentThread().getName() + " waiting (buffer full)");
                notFull.await();  // Wait until buffer has space
            }
            queue.add(item);
            System.out.println(Thread.currentThread().getName() + " produced: " + item);
            notEmpty.signal();  // Notify consumers
        } finally {
            lock.unlock();
        }
    }
    
    public String consume() throws InterruptedException {
        lock.lock();
        try {
            // while loop handles spurious wakeups
            while (queue.isEmpty()) {
                System.out.println(Thread.currentThread().getName() + " waiting (buffer empty)");
                notEmpty.await();  // Wait until buffer has items
            }
            String item = queue.poll();
            notFull.signal();  // Notify producers
            return item;
        } finally {
            lock.unlock();
        }
    }
}
```

#### Interview Questions on Spurious Wakeups

**Q1: Why use `while` instead of `if` with condition variables?**
A: Because spurious wakeups can occur, waking threads without a signal. A `while` loop re-checks the condition after waking, ensuring the predicate is still true.

**Q2: What causes spurious wakeups?**
A: OS-level interrupts, system scheduling decisions, and platform-specific JVM implementations can cause threads to wake without explicit notification.

**Q3: Is spurious wakeup a bug?**
A: No, it's documented behavior in Java and POSIX standards. It's a performance trade-off where preventing spurious wakeups would be more expensive.

**Q4: Can spurious wakeup cause data corruption?**
A: No, if you follow the pattern correctly (while-loop + condition check). The lock ensures only one thread modifies data at a time.

**Q5: How do you defend against spurious wakeups in code?**
A: Always use `while(condition) { lock.await(); }` instead of `if(condition) { lock.await(); }`.

---

## 🛠️ Synchronization Utilities

### 7. **CountDownLatch**
- **One-time** synchronization barrier
- **Count decrements** from N to 0
- **Threads wait** until count reaches 0
- **Use case**: Wait for multiple tasks completion

```java
CountDownLatch latch = new CountDownLatch(3);
latch.countDown(); // decrement count
latch.await();     // wait for count to reach 0
```

### 8. **CyclicBarrier**
- **Reusable** synchronization barrier
- **Threads wait** at barrier point
- **Barrier breaks** when all threads arrive
- **Use case**: Parallel processing phases

```java
CyclicBarrier barrier = new CyclicBarrier(3);
barrier.await(); // wait for all threads
```

### 9. **Phaser (Java 7+)**
- **Flexible** synchronization barrier
- **Dynamic** number of parties
- **Multiple phases** support
- **Advanced** alternative to CyclicBarrier

```java
Phaser phaser = new Phaser(3);
phaser.arriveAndAwaitAdvance(); // wait for phase completion
```

### 10. **Exchanger**
- **Two-thread** data exchange
- **Synchronous** exchange point
- **Bidirectional** communication
- **Use case**: Producer-consumer pairs

```java
Exchanger<String> exchanger = new Exchanger<>();
String data = exchanger.exchange("myData");
```


## 🔑 Lock Methods & Exceptions

### ReentrantLock Methods
```java
lock()                    // Acquire lock (blocks if unavailable)
tryLock()                 // Try to acquire, returns boolean
tryLock(long, TimeUnit)   // Try with timeout
lockInterruptibly()       // Acquire, throws InterruptedException if interrupted
unlock()                  // Release lock
newCondition()            // Create condition variable
getHoldCount()            // Get reentrant count for current thread
isLocked()                // Check if lock is held
isFair()                  // Check if lock is fair
```

### ReadWriteLock Methods
```java
readLock()                // Get read lock
writeLock()               // Get write lock
```

### StampedLock Methods
```java
readLock()                // Acquire read lock, returns stamp
writeLock()               // Acquire write lock, returns stamp
tryOptimisticRead()       // Non-blocking optimistic read, returns stamp
validate(stamp)           // Validate stamp after optimistic read
unlockRead(stamp)         // Release read lock
unlockWrite(stamp)        // Release write lock
```

### Common Exceptions
```java
InterruptedException     // Thread interrupted while waiting
IllegalMonitorStateException  // unlock() called without lock held
TimeoutException         // Timeout occurred during tryLock()
```

---

## 🎚️ Phaser - Advanced Synchronization

**Phaser** is a more flexible alternative to `CountDownLatch` and `CyclicBarrier`. It supports dynamic party registration and multiple phases.

### Key Methods
```java
new Phaser(partyCount)           // Initialize with party count
register()                       // Register a party (must be called by party)
bulkRegister(count)              // Register multiple parties at once
arrive()                         // Signal arrival, continue immediately
arriveAndAwaitAdvance()          // Arrive and wait for all parties
arriveAndDeregister()            // Arrive and leave the phaser
getPhase()                       // Get current phase number
getArrivedParties()              // Count of arrived parties
getUnarrivedParties()            // Count of unarrived parties
onAdvance(phase, registeredParties)  // Override for custom action
forceTerminate()                 // Force phaser to terminate
isTerminated()                   // Check if terminated
```

### Example 1: Basic Task Coordination (Like CountDownLatch)
```java
Phaser phaser = new Phaser(3);

executor.submit(() -> {
    Thread.sleep(1000);
    System.out.println("Task 1 done");
    phaser.arrive();  // Signal completion
});

phaser.awaitAdvance(0);  // Wait for phase 0 to complete
System.out.println("All tasks completed");
```

### Example 2: Multi-Phase Barrier (Like CyclicBarrier, Reusable)
```java
Phaser phaser = new Phaser(3);

for (int i = 0; i < 3; i++) {
    executor.submit(() -> {
        while (!phaser.isTerminated()) {
            // Phase work
            System.out.println("Working on phase " + phaser.getPhase());
            phaser.arriveAndAwaitAdvance();  // Sync point
        }
    });
}
```

### Example 3: Dynamic Registration (Self-Registering Parties)
```java
Phaser phaser = new Phaser(1);  // Start with 1 (main thread)

executor.submit(() -> {
    phaser.register();  // Register self
    // Do work
    phaser.arrive();
});

executor.submit(() -> {
    phaser.register();  // Register self
    // Do work
    phaser.arrive();
});

phaser.arriveAndAwaitAdvance();  // Wait for all registered parties
phaser.bulkRegister(2);          // Add more parties later if needed
```

### Example 4: Arrive and Deregister (Flexible Exit)
```java
Phaser phaser = new Phaser(3);

executor.submit(() -> {
    // Phase 1
    phaser.arriveAndAwaitAdvance();
    
    // Phase 2 - don't participate anymore
    System.out.println("Leaving phaser");
    phaser.arriveAndDeregister();  // Signal and exit
});

// Other threads continue with more phases
```

### Example 5: Custom Barrier Action
```java
Phaser phaser = new Phaser(3) {
    @Override
    protected boolean onAdvance(int phase, int registeredParties) {
        System.out.println("Phase " + phase + " completed");
        return registeredParties == 0;  // Terminate when no parties left
    }
};

executor.submit(() -> phaser.arriveAndAwaitAdvance());
```

---

## 📊 Phaser vs CountDownLatch vs CyclicBarrier

| Feature | CountDownLatch | CyclicBarrier | Phaser |
|---------|---|---|---|
| **Reusable** | ❌ No (one-time) | ✅ Yes | ✅ Yes |
| **Dynamic parties** | ❌ No | ❌ No | ✅ Yes |
| **Barrier action** | ❌ No | ✅ Yes | ✅ Yes |
| **Multiple phases** | ❌ No | ✅ Limited | ✅ Yes |
| **Self-registration** | ❌ No | ❌ No | ✅ Yes |
| **Flexibility** | Low | Medium | High |

---


## 📊 Lock Comparison Table

| Lock Type | Fairness | Interruptible | Timeout | Condition | Performance |
|-----------|----------|---------------|---------|-----------|-------------|
| synchronized | No | No | No | Built-in | Good |
| ReentrantLock | Yes/No | Yes | Yes | Multiple | Good |
| ReadWriteLock | Yes/No | Yes | Yes | Multiple | Better (reads) |
| StampedLock | No | Yes | Yes | No | Best |
| Semaphore | Yes/No | Yes | Yes | No | Good |

---

## 🎯 When to Use Which Lock

| Scenario | Lock Type | Reason |
|----------|-----------|--------|
| Simple object-level thread safety | `synchronized` | Built-in, easy to use |
| Locking across multiple objects | `ReentrantLock` | Advanced features, fairness/timeout |
| Many reads, few writes (high read concurrency) | `ReadWriteLock` | Multiple readers, better performance |
| Optimistic concurrency, minimal lock overhead | `StampedLock` | Optimistic reads, best performance |
| Limit concurrent access (resource pools) | `Semaphore` | Permit-based access control |
| Thread communication (no monitor lock) | `Condition` | Signal/await mechanism |
| One-time task coordination | `CountDownLatch` | One-way synchronization barrier |
| Reusable parallel processing phases | `CyclicBarrier` | Reusable barrier for phases |
| Complex/dynamic synchronization | `Phaser` | Flexible phases, dynamic parties |
| Bidirectional data exchange | `Exchanger` | Two-thread synchronous exchange |


## 💡 Best Practices

### ✅ Do's
- Always use try-finally with explicit locks
- Use fair locks for avoiding starvation
- Prefer ReadWriteLock for read-heavy operations
- Use StampedLock for high-performance scenarios
- Choose appropriate synchronization utility

### ❌ Don'ts
- Don't forget to unlock in finally block
- Don't use synchronized with explicit locks
- Don't hold locks longer than necessary
- Don't ignore interrupted exceptions
- Don't use wrong lock type for scenario

---

## 🔧 Quick Reference Code Snippets

### Basic ReentrantLock Pattern
```java
Lock lock = new ReentrantLock();
lock.lock();
try {
    // critical section
} finally {
    lock.unlock();
}
```

### ReadWriteLock Pattern
```java
ReadWriteLock rwLock = new ReentrantReadWriteLock();
// Read operation
rwLock.readLock().lock();
try {
    // read data
} finally {
    rwLock.readLock().unlock();
}
```

### StampedLock Optimistic Read
```java
StampedLock lock = new StampedLock();
long stamp = lock.tryOptimisticRead();
// read operation
if (!lock.validate(stamp)) {
    // fallback to read lock
}
```

### Semaphore Resource Control
```java
Semaphore semaphore = new Semaphore(permits);
semaphore.acquire();
try {
    // use resource
} finally {
    semaphore.release();
}
```

---

# 🎪 Complete Code Examples

## 1. ReentrantLock with Fairness

```java
import java.util.concurrent.locks.ReentrantLock;

public class FairLockExample {
    public static void main(String[] args) {
        SharedResource resource = new SharedResource();
        
        // Create multiple threads
        for (int i = 0; i < 5; i++) {
            Thread thread = new Thread(() -> resource.accessResource(), "Thread-" + i);
            thread.start();
        }
    }
}

class SharedResource {
    private final ReentrantLock lock = new ReentrantLock(true); // Fair lock
    
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

## 2. ReadWriteLock for Cache Implementation

```java
import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.locks.ReadWriteLock;
import java.util.concurrent.locks.ReentrantReadWriteLock;

public class CacheExample {
    public static void main(String[] args) {
        Cache cache = new Cache();
        
        // Reader threads
        for (int i = 0; i < 3; i++) {
            Thread reader = new Thread(() -> {
                for (int j = 0; j < 5; j++) {
                    System.out.println("Read: " + cache.get("key" + j));
                    try { Thread.sleep(100); } catch (InterruptedException e) {}
                }
            }, "Reader-" + i);
            reader.start();
        }
        
        // Writer thread
        Thread writer = new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                cache.put("key" + i, "value" + i);
                try { Thread.sleep(500); } catch (InterruptedException e) {}
            }
        }, "Writer");
        writer.start();
    }
}

class Cache {
    private final Map<String, String> cache = new HashMap<>();
    private final ReadWriteLock lock = new ReentrantReadWriteLock();
    
    public String get(String key) {
        lock.readLock().lock();
        try {
            System.out.println(Thread.currentThread().getName() + " reading key: " + key);
            Thread.sleep(100);
            return cache.get(key);
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
            System.out.println(Thread.currentThread().getName() + " writing key: " + key);
            cache.put(key, value);
            Thread.sleep(200);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        } finally {
            lock.writeLock().unlock();
        }
    }
}
```

## 3. StampedLock Optimistic Read Example

```java
import java.util.concurrent.locks.StampedLock;

public class OptimisticReadExample {
    public static void main(String[] args) {
        Point point = new Point(10, 20);
        
        // Reader thread with optimistic read
        Thread reader = new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                System.out.println("Distance: " + point.distanceFromOrigin());
                try { Thread.sleep(100); } catch (InterruptedException e) {}
            }
        }, "Reader");
        
        // Writer thread
        Thread writer = new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                point.move(i, i);
                try { Thread.sleep(300); } catch (InterruptedException e) {}
            }
        }, "Writer");
        
        reader.start();
        writer.start();
    }
}

class Point {
    private double x, y;
    private final StampedLock lock = new StampedLock();
    
    public Point(double x, double y) {
        this.x = x;
        this.y = y;
    }
    
    public double distanceFromOrigin() {
        long stamp = lock.tryOptimisticRead();
        double curX = x, curY = y;
        
        if (!lock.validate(stamp)) {
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

## 4. Semaphore Connection Pool

```java
import java.util.concurrent.Semaphore;

public class ConnectionPoolExample {
    public static void main(String[] args) {
        ConnectionPool pool = new ConnectionPool(3);
        
        // Create multiple clients
        for (int i = 0; i < 10; i++) {
            Thread client = new Thread(() -> {
                try {
                    Connection conn = pool.getConnection();
                    // Use connection
                    Thread.sleep(2000);
                    pool.releaseConnection(conn);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            }, "Client-" + i);
            client.start();
        }
    }
}

class ConnectionPool {
    private final Semaphore available;
    private final Connection[] connections;
    private final boolean[] used;
    
    public ConnectionPool(int size) {
        available = new Semaphore(size, true);
        connections = new Connection[size];
        used = new boolean[size];
        
        for (int i = 0; i < size; i++) {
            connections[i] = new Connection("Connection-" + i);
        }
    }
    
    public Connection getConnection() throws InterruptedException {
        available.acquire();
        return getNextAvailableConnection();
    }
    
    public void releaseConnection(Connection connection) {
        if (markAsUnused(connection)) {
            available.release();
        }
    }
    
    private synchronized Connection getNextAvailableConnection() {
        for (int i = 0; i < used.length; i++) {
            if (!used[i]) {
                used[i] = true;
                System.out.println(Thread.currentThread().getName() + 
                    " acquired " + connections[i].getName());
                return connections[i];
            }
        }
        return null;
    }
    
    private synchronized boolean markAsUnused(Connection connection) {
        for (int i = 0; i < connections.length; i++) {
            if (connections[i] == connection) {
                if (used[i]) {
                    used[i] = false;
                    System.out.println(Thread.currentThread().getName() + 
                        " released " + connection.getName());
                    return true;
                }
            }
        }
        return false;
    }
}

class Connection {
    private final String name;
    
    public Connection(String name) {
        this.name = name;
    }
    
    public String getName() {
        return name;
    }
}
```

## 5. CountDownLatch Task Coordination

```java
import java.util.concurrent.CountDownLatch;

public class TaskCoordinationExample {
    public static void main(String[] args) throws InterruptedException {
        int numTasks = 5;
        CountDownLatch latch = new CountDownLatch(numTasks);
        
        // Start multiple tasks
        for (int i = 0; i < numTasks; i++) {
            Thread task = new Thread(new Task(latch, i), "Task-" + i);
            task.start();
        }
        
        System.out.println("Waiting for all tasks to complete...");
        latch.await(); // Wait for all tasks to complete
        System.out.println("All tasks completed!");
    }
}

class Task implements Runnable {
    private final CountDownLatch latch;
    private final int taskId;
    
    public Task(CountDownLatch latch, int taskId) {
        this.latch = latch;
        this.taskId = taskId;
    }
    
    @Override
    public void run() {
        try {
            // Simulate task work
            Thread.sleep((taskId + 1) * 1000);
            System.out.println("Task " + taskId + " completed");
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        } finally {
            latch.countDown(); // Signal completion
        }
    }
}
```

## 6. CyclicBarrier Parallel Processing

```java
import java.util.concurrent.CyclicBarrier;

public class ParallelProcessingExample {
    public static void main(String[] args) {
        int numThreads = 4;
        CyclicBarrier barrier = new CyclicBarrier(numThreads, () -> {
            System.out.println("All threads finished phase, starting next phase");
        });
        
        for (int i = 0; i < numThreads; i++) {
            Thread worker = new Thread(new Worker(barrier, i), "Worker-" + i);
            worker.start();
        }
    }
}

class Worker implements Runnable {
    private final CyclicBarrier barrier;
    private final int workerId;
    
    public Worker(CyclicBarrier barrier, int workerId) {
        this.barrier = barrier;
        this.workerId = workerId;
    }
    
    @Override
    public void run() {
        try {
            for (int phase = 1; phase <= 3; phase++) {
                // Do work for current phase
                System.out.println("Worker " + workerId + " working on phase " + phase);
                Thread.sleep((workerId + 1) * 500);
                
                System.out.println("Worker " + workerId + " finished phase " + phase);
                barrier.await(); // Wait for all workers to finish phase
            }
        } catch (Exception e) {
            Thread.currentThread().interrupt();
        }
    }
}
```

## 7. Exchanger Data Exchange

```java
import java.util.concurrent.Exchanger;

public class DataExchangeExample {
    public static void main(String[] args) {
        Exchanger<String> exchanger = new Exchanger<>();
        
        Thread producer = new Thread(() -> {
            try {
                for (int i = 0; i < 5; i++) {
                    String data = "Data-" + i;
                    System.out.println("Producer sending: " + data);
                    String received = exchanger.exchange(data);
                    System.out.println("Producer received: " + received);
                    Thread.sleep(1000);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }, "Producer");
        
        Thread consumer = new Thread(() -> {
            try {
                for (int i = 0; i < 5; i++) {
                    String ack = "ACK-" + i;
                    String received = exchanger.exchange(ack);
                    System.out.println("Consumer received: " + received);
                    System.out.println("Consumer sending: " + ack);
                    Thread.sleep(1000);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }, "Consumer");
        
        producer.start();
        consumer.start();
    }
}
```

---

## 🎯 Key Takeaways

1. **Choose the right lock** for your use case
2. **Always use try-finally** with explicit locks
3. **Consider fairness** for avoiding starvation
4. **Use ReadWriteLock** for read-heavy operations
5. **StampedLock** offers best performance for read-heavy scenarios
6. **Semaphore** is perfect for resource limiting
7. **CountDownLatch** for one-time coordination
8. **CyclicBarrier** for reusable synchronization points
9. **Exchanger** for bidirectional data exchange

Remember: The key to effective multithreading is choosing the right synchronization mechanism for your specific use case!