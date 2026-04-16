# Java Synchronization Utilities — CountDownLatch, CyclicBarrier, Phaser, Exchanger

---

## CountDownLatch

A one-time synchronization barrier. Initialized with a count N. Threads call `await()` to block until the count reaches zero. Other threads call `countDown()` to decrement it. Once zero, it cannot be reset.

```java
CountDownLatch latch = new CountDownLatch(3);
latch.countDown(); // decrement count by 1
latch.await();     // block until count reaches 0
latch.await(5, TimeUnit.SECONDS); // block with timeout
```

**Use case:** wait for N parallel tasks to all finish before proceeding.

### CountDownLatch — Full Example

```java
import java.util.concurrent.CountDownLatch;

public class TaskCoordinationExample {
    public static void main(String[] args) throws InterruptedException {
        int numTasks = 5;
        CountDownLatch latch = new CountDownLatch(numTasks);

        for (int i = 0; i < numTasks; i++) {
            new Thread(new Task(latch, i), "Task-" + i).start();
        }

        System.out.println("Waiting for all tasks...");
        latch.await(); // blocks until all 5 tasks call countDown()
        System.out.println("All tasks completed!");
    }
}

class Task implements Runnable {
    private final CountDownLatch latch;
    private final int taskId;

    Task(CountDownLatch latch, int taskId) {
        this.latch  = latch;
        this.taskId = taskId;
    }

    @Override
    public void run() {
        try {
            Thread.sleep((taskId + 1) * 1000L);
            System.out.println("Task " + taskId + " completed");
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        } finally {
            latch.countDown(); // always signal, even on exception
        }
    }
}
```

---

## CyclicBarrier

A reusable barrier. A fixed number of threads (parties) must all call `await()` before any of them is allowed to proceed. After all parties arrive, the barrier resets automatically for the next cycle.

An optional **barrier action** (a `Runnable`) runs once when the last party arrives, before threads are released.

```java
CyclicBarrier barrier = new CyclicBarrier(3);           // 3 parties required
CyclicBarrier barrier = new CyclicBarrier(3, () -> {    // with barrier action
    System.out.println("All arrived — starting next phase");
});

barrier.await(); // wait for all parties
```

**Use case:** parallel processing in phases — all workers must finish phase N before any starts phase N+1.

### CyclicBarrier — Full Example

```java
import java.util.concurrent.CyclicBarrier;

public class ParallelProcessingExample {
    public static void main(String[] args) {
        int numWorkers = 4;
        CyclicBarrier barrier = new CyclicBarrier(numWorkers,
            () -> System.out.println("--- All workers finished phase, advancing ---"));

        for (int i = 0; i < numWorkers; i++) {
            new Thread(new Worker(barrier, i), "Worker-" + i).start();
        }
    }
}

class Worker implements Runnable {
    private final CyclicBarrier barrier;
    private final int workerId;

    Worker(CyclicBarrier barrier, int workerId) {
        this.barrier  = barrier;
        this.workerId = workerId;
    }

    @Override
    public void run() {
        try {
            for (int phase = 1; phase <= 3; phase++) {
                System.out.println("Worker-" + workerId + " working on phase " + phase);
                Thread.sleep((workerId + 1) * 500L);
                System.out.println("Worker-" + workerId + " finished phase " + phase);
                barrier.await(); // wait for all workers before next phase
            }
        } catch (Exception e) {
            Thread.currentThread().interrupt();
        }
    }
}
```

---

## Phaser (Java 7+)

A more flexible barrier that supports:
- **Reuse** across multiple phases (like `CyclicBarrier`)
- **Dynamic parties** — threads can register and deregister at runtime
- **Custom advance logic** via `onAdvance()`

```java
Phaser phaser = new Phaser(partyCount);
```

### Key Methods

```java
register()                       // register one new party
bulkRegister(int count)          // register multiple parties at once
arrive()                         // signal arrival, do NOT wait
arriveAndAwaitAdvance()          // signal arrival, wait for all others
arriveAndDeregister()            // signal arrival, then leave permanently
awaitAdvance(int phase)          // wait for a specific phase to complete
getPhase()                       // current phase number
getArrivedParties()              // how many have arrived this phase
getUnarrivedParties()            // how many still expected
forceTerminate()                 // shut the phaser down
isTerminated()                   // whether the phaser is terminated
```

### Example 1 — One-Shot Coordination (like CountDownLatch)

```java
Phaser phaser = new Phaser(3); // 3 parties

executor.submit(() -> {
    Thread.sleep(1000);
    System.out.println("Task 1 done");
    phaser.arrive(); // signal without waiting
});
// ... (2 more tasks)

phaser.awaitAdvance(0); // wait for phase 0 to complete
System.out.println("All tasks done");
```

### Example 2 — Multi-Phase Barrier (like CyclicBarrier, Reusable)

```java
Phaser phaser = new Phaser(3);

for (int i = 0; i < 3; i++) {
    executor.submit(() -> {
        while (!phaser.isTerminated()) {
            System.out.println("Working on phase " + phaser.getPhase());
            phaser.arriveAndAwaitAdvance(); // sync point per phase
        }
    });
}
```

### Example 3 — Dynamic Registration (Self-Registering Parties)

```java
Phaser phaser = new Phaser(1); // 1 = main thread

executor.submit(() -> {
    phaser.register(); // thread registers itself
    // do work
    phaser.arrive();
});

executor.submit(() -> {
    phaser.register();
    // do work
    phaser.arrive();
});

phaser.arriveAndAwaitAdvance(); // main thread waits for all
```

### Example 4 — Arrive and Deregister (Flexible Exit)

```java
Phaser phaser = new Phaser(3);

executor.submit(() -> {
    phaser.arriveAndAwaitAdvance(); // participates in phase 1
    System.out.println("Leaving after phase 1");
    phaser.arriveAndDeregister();   // exits permanently after phase 1
});
// remaining threads continue with subsequent phases
```

### Example 5 — Custom Barrier Action via onAdvance()

```java
Phaser phaser = new Phaser(3) {
    @Override
    protected boolean onAdvance(int phase, int registeredParties) {
        System.out.println("Phase " + phase + " complete, parties: " + registeredParties);
        return registeredParties == 0; // return true to terminate the phaser
    }
};
```

---

## CountDownLatch vs CyclicBarrier vs Phaser

| Feature | `CountDownLatch` | `CyclicBarrier` | `Phaser` |
|---|---|---|---|
| Reusable | No (one-time) | Yes | Yes |
| Dynamic parties | No | No | Yes |
| Barrier action | No | Yes | Yes (`onAdvance`) |
| Multiple phases | No | Limited | Yes |
| Self-registration | No | No | Yes |
| Who decrements | Anyone | Parties themselves | Parties themselves |
| Flexibility | Low | Medium | High |

**Decision guide:**
- One-time wait for N tasks → `CountDownLatch`
- Fixed group of threads, repeated phases → `CyclicBarrier`
- Dynamic participants, multiple phases, complex coordination → `Phaser`

---

## Exchanger

A synchronisation point at which exactly **two** threads can swap objects. Both threads block on `exchange()` until the other arrives, then each receives the other's object.

```java
Exchanger<String> exchanger = new Exchanger<>();

// Thread A
String received = exchanger.exchange("from-A"); // blocks until Thread B arrives

// Thread B
String received = exchanger.exchange("from-B"); // Thread A gets "from-B", Thread B gets "from-A"
```

**Use case:** producer-consumer pairs where each side hands off a buffer and receives an empty one in return; bidirectional pipeline stages.

### Exchanger — Full Example

```java
import java.util.concurrent.Exchanger;

public class DataExchangeExample {
    public static void main(String[] args) {
        Exchanger<String> exchanger = new Exchanger<>();

        new Thread(() -> {
            try {
                for (int i = 0; i < 5; i++) {
                    String data = "Data-" + i;
                    System.out.println("Producer sending: " + data);
                    String ack = exchanger.exchange(data);  // blocks until consumer arrives
                    System.out.println("Producer received: " + ack);
                    Thread.sleep(1000);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }, "Producer").start();

        new Thread(() -> {
            try {
                for (int i = 0; i < 5; i++) {
                    String ack = "ACK-" + i;
                    String data = exchanger.exchange(ack);  // blocks until producer arrives
                    System.out.println("Consumer received: " + data);
                    System.out.println("Consumer sending:  " + ack);
                    Thread.sleep(1000);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }, "Consumer").start();
    }
}
```

---

## When to Use Which Utility

| Scenario | Use |
|---|---|
| Wait for N parallel tasks to finish (one-time) | `CountDownLatch` |
| Fixed threads, multiple synchronised phases | `CyclicBarrier` |
| Dynamic participants, complex phased coordination | `Phaser` |
| Two-thread bidirectional data swap | `Exchanger` |

---

## Best Practices

Always call `countDown()` / `arrive()` in a `finally` block so a failing task does not leave other threads waiting forever:

```java
try {
    doWork();
} catch (Exception e) {
    log.error("Task failed", e);
} finally {
    latch.countDown(); // always release — even on failure
}
```

For `CyclicBarrier`, handle `BrokenBarrierException` — it signals that another party was interrupted or timed out, making the barrier unusable. Log the failure and abort the phase.
