# Java Synchronization Utilities


## Table of Contents

- [1. CountDownLatch](#1-countdownlatch)
  - [1.1 How It Works](#11-how-it-works)
  - [1.2 API](#12-api)
  - [1.3 Full Example](#13-full-example)
- [2. CyclicBarrier](#2-cyclicbarrier)
  - [2.1 How It Works](#21-how-it-works)
  - [2.2 API](#22-api)
  - [2.3 Full Example](#23-full-example)
- [3. Phaser](#3-phaser)
  - [3.1 How It Works](#31-how-it-works)
  - [3.2 API](#32-api)
  - [3.3 Example 1 — One-Shot Coordination](#33-example-1--one-shot-coordination)
  - [3.4 Example 2 — Multi-Phase Barrier](#34-example-2--multi-phase-barrier)
  - [3.5 Example 3 — Dynamic Registration](#35-example-3--dynamic-registration)
  - [3.6 Example 4 — Arrive and Deregister](#36-example-4--arrive-and-deregister)
  - [3.7 Example 5 — Custom Barrier Action](#37-example-5--custom-barrier-action)
- [4. Exchanger](#4-exchanger)
  - [4.1 How It Works](#41-how-it-works)
  - [4.2 API](#42-api)
  - [4.3 Full Example](#43-full-example)
- [5. Comparison and Decision Guide](#5-comparison-and-decision-guide)
  - [5.1 Feature Comparison](#51-feature-comparison)
  - [5.2 When to Use Which](#52-when-to-use-which)
- [6. Best Practices](#6-best-practices)

---

## 1. CountDownLatch

### 1.1 How It Works

> `CountDownLatch` is a synchronization utility in Java used to make one or more threads wait until some other threads complete their work.

> A **one-time synchronization barrier**.

- Initialized with a count `N`.
- Threads call `await()` to block until the count reaches zero.
- Other threads call `countDown()` to decrement the count.
- Once zero, it **cannot be reset**.

Think of it as a gate that opens only after N events have happened.

### 1.2 API

```java
CountDownLatch latch = new CountDownLatch(3);

latch.countDown();                      // decrement count by 1
latch.await();                          // block until count reaches 0
latch.await(5, TimeUnit.SECONDS);       // block with timeout
latch.getCount();                       // check remaining count
```

### 1.3 Full Example

```java
import java.util.concurrent.CountDownLatch;

public class TaskCoordinationExample
{
    public static void main(String[] args) throws InterruptedException
    {
        int numTasks = 5;
        CountDownLatch latch = new CountDownLatch(numTasks);

        for (int i = 0; i < numTasks; i++)
        {
            new Thread(new Task(latch, i), "Task-" + i).start();
        }

        System.out.println("Waiting for all tasks...");
        latch.await(); // main thread blocks here until all 5 tasks finish
        System.out.println("All tasks completed!");
    }
}

class Task implements Runnable
{
    private final CountDownLatch latch;
    private final int taskId;

    Task(CountDownLatch latch, int taskId)
    {
        this.latch  = latch;
        this.taskId = taskId;
    }

    @Override
    public void run()
    {
        try
        {
            Thread.sleep((taskId + 1) * 1000L); // simulate work
            System.out.println("Task " + taskId + " completed");
        }
        catch (InterruptedException e)
        {
            Thread.currentThread().interrupt();
        }
        finally
        {
            latch.countDown(); // always signal, even if task threw an exception
        }
    }
}
```

---

## 2. CyclicBarrier

### 2.1 How It Works

A **reusable barrier** for a fixed group of threads.

- A fixed number of threads (called **parties**) must all call `await()` before any of them proceeds.
- After all parties arrive, the barrier **resets automatically** for the next cycle.
- An optional **barrier action** (a `Runnable`) runs once when the last party arrives, before threads are released.

Think of it as a checkpoint — all runners must reach it before any continues.

> `CyclicBarrier` makes multiple threads wait for each other at a common point. In this example, all workers must complete one phase before any worker starts the next phase. Fast threads wait at `barrier.await()` until the slowest thread arrives.`

### 2.2 API

```java
CyclicBarrier barrier = new CyclicBarrier(3);           // 3 parties required

// with a barrier action that runs when all parties arrive
CyclicBarrier barrier = new CyclicBarrier(3, () ->
{
    System.out.println("All arrived — starting next phase");
});

barrier.await();                        // wait for all parties
barrier.await(5, TimeUnit.SECONDS);     // wait with timeout
barrier.getNumberWaiting();             // how many are currently waiting
barrier.getParties();                   // total parties required
barrier.reset();                        // manually reset the barrier
barrier.isBroken();                     // true if barrier was broken (interrupted/timeout)
```

### 2.3 Full Example

```java
import java.util.concurrent.CyclicBarrier;

public class ParallelProcessingExample
{
    public static void main(String[] args)
    {
        int numWorkers = 4;

        // barrier action runs after all workers finish each phase
        CyclicBarrier barrier = new CyclicBarrier(
            numWorkers,
            () -> System.out.println("--- All workers finished phase, advancing ---")
        );

        for (int i = 0; i < numWorkers; i++)
        {
            new Thread(new Worker(barrier, i), "Worker-" + i).start();
        }
    }
}

class Worker implements Runnable
{
    private final CyclicBarrier barrier;
    private final int workerId;

    Worker(CyclicBarrier barrier, int workerId)
    {
        this.barrier  = barrier;
        this.workerId = workerId;
    }

    @Override
    public void run()
    {
        try
        {
            for (int phase = 1; phase <= 3; phase++)
            {
                System.out.println("Worker-" + workerId + " working on phase " + phase);
                Thread.sleep((workerId + 1) * 500L); // simulate phase work
                System.out.println("Worker-" + workerId + " finished phase " + phase);

                barrier.await(); // all workers must reach here before any continues
                // all the thread execute 0 iteration and stop and 1 stop 2 stop
            }
        }
        catch (Exception e)
        {
            Thread.currentThread().interrupt();
        }
    }
}
```

---

## 3. Phaser

### 3.1 How It Works

> `Phaser` is a flexible synchronization utility that coordinates multiple threads in multiple phases and supports dynamic registration and deregistration of participants.


- Supports **multiple phases** like `CyclicBarrier`.
- Supports **dynamic parties** — threads can register and deregister at runtime.
- Supports **custom phase advance logic** via `onAdvance()`.
- The most powerful of the three synchronization barriers.

```java
Phaser phaser = new Phaser(partyCount);
```

### Why Phaser?

`CyclicBarrier` has fixed number of threads.

But `Phaser` allows:

* dynamic registration
* dynamic removal
* multiple phases

### 3.2 API

| Method | Description |
|---|---|
| `register()` | Register one new party |
| `bulkRegister(int count)` | Register multiple parties at once |
| `arrive()` | Signal arrival, do NOT wait |
| `arriveAndAwaitAdvance()` | Signal arrival, wait for all others |
| `arriveAndDeregister()` | Signal arrival, then leave permanently |
| `awaitAdvance(int phase)` | Wait for a specific phase to complete |
| `getPhase()` | Current phase number |
| `getArrivedParties()` | How many have arrived this phase |
| `getUnarrivedParties()` | How many are still expected |
| `forceTerminate()` | Shut the phaser down |
| `isTerminated()` | Whether the phaser has been terminated |

### 3.3 Example 1 — One-Shot Coordination

Works like a `CountDownLatch`. Tasks signal arrival without waiting; the main thread waits for all.

```java
Phaser phaser = new Phaser(3); // 3 parties

// each task signals when done, without blocking itself
executor.submit(() ->
{
    Thread.sleep(1000);
    System.out.println("Task 1 done");
    phaser.arrive(); // signal arrival but do not wait
});

// ... (2 more tasks similar to above)

phaser.awaitAdvance(0); // main thread waits for phase 0 to complete
System.out.println("All tasks done");
```

### 3.4 Example 2 — Multi-Phase Barrier

Works like a `CyclicBarrier`. All threads sync at the end of each phase before moving to the next.

```java
Phaser phaser = new Phaser(3);

for (int i = 0; i < 3; i++)
{
    executor.submit(() ->
    {
        while (!phaser.isTerminated())
        {
            System.out.println("Working on phase " + phaser.getPhase());
            phaser.arriveAndAwaitAdvance(); // sync point — all must arrive before any continues
        }
    });
}
```

### 3.5 Example 3 — Dynamic Registration

Threads register themselves at runtime instead of being pre-registered.

```java
Phaser phaser = new Phaser(1); // 1 = main thread only at start

executor.submit(() ->
{
    phaser.register(); // thread registers itself before doing any work
    // do work
    phaser.arrive();   // signal completion
});

executor.submit(() ->
{
    phaser.register();
    // do work
    phaser.arrive();
});

phaser.arriveAndAwaitAdvance(); // main thread waits for all registered parties
```

### 3.6 Example 4 — Arrive and Deregister

A thread participates in one phase, then permanently exits. Remaining threads continue future phases.

```java
Phaser phaser = new Phaser(3);

executor.submit(() ->
{
    phaser.arriveAndAwaitAdvance(); // participates in phase 1
    System.out.println("Leaving after phase 1");
    phaser.arriveAndDeregister();   // exits permanently — no longer counted in future phases
});

// remaining 2 threads continue with phases 2, 3, etc.
```

### 3.7 Example 5 — Custom Barrier Action

Override `onAdvance()` to run custom logic at the end of each phase. Return `true` to terminate the phaser.

```java
Phaser phaser = new Phaser(3)
{
    @Override
    protected boolean onAdvance(int phase, int registeredParties)
    {
        System.out.println("Phase " + phase + " complete. Parties remaining: " + registeredParties);
        return registeredParties == 0; // terminate when no parties remain
    }
};
```

---

## 4. Exchanger

### 4.1 How It Works

> `Exchanger` is a synchronization utility where two threads meet and exchange data objects at a synchronization point using the `exchange()` method.

- Both threads block on `exchange()` until the other arrives.
- Each thread then receives what the other handed in.
- Useful for **producer-consumer pairs** where each side hands off a full buffer and receives an empty one.

### 4.2 API

```java
Exchanger<String> exchanger = new Exchanger<>();

// Thread A hands "from-A" and receives what Thread B handed
String received = exchanger.exchange("from-A");

// Thread B hands "from-B" and receives what Thread A handed
String received = exchanger.exchange("from-B");

// with timeout — throws TimeoutException if other thread does not arrive in time
String received = exchanger.exchange("from-A", 5, TimeUnit.SECONDS);
```

### 4.3 Full Example

```java
import java.util.concurrent.Exchanger;

public class DataExchangeExample
{
    public static void main(String[] args)
    {
        Exchanger<String> exchanger = new Exchanger<>();

        // Producer: sends data, receives acknowledgement
        new Thread(() ->
        {
            try
            {
                for (int i = 0; i < 5; i++)
                {
                    String data = "Data-" + i;
                    System.out.println("Producer sending: " + data);

                    String ack = exchanger.exchange(data); // blocks until Consumer arrives

                    System.out.println("Producer received: " + ack);
                    Thread.sleep(1000);
                }
            }
            catch (InterruptedException e)
            {
                Thread.currentThread().interrupt();
            }
        }, "Producer").start();

        // Consumer: sends acknowledgement, receives data
        new Thread(() ->
        {
            try
            {
                for (int i = 0; i < 5; i++)
                {
                    String ack = "ACK-" + i;

                    String data = exchanger.exchange(ack); // blocks until Producer arrives

                    System.out.println("Consumer received: " + data);
                    System.out.println("Consumer sending:  " + ack);
                    Thread.sleep(1000);
                }
            }
            catch (InterruptedException e)
            {
                Thread.currentThread().interrupt();
            }
        }, "Consumer").start();
    }
}
```

---

## 5. Comparison and Decision Guide

### 5.1 Feature Comparison

| Feature | CountDownLatch | CyclicBarrier | Phaser |
|---|---|---|---|
| Reusable | No (one-time) | Yes | Yes |
| Dynamic parties | No | No | Yes |
| Barrier action | No | Yes | Yes (`onAdvance`) |
| Multiple phases | No | Limited | Yes |
| Self-registration | No | No | Yes |
| Who signals | Anyone | Parties themselves | Parties themselves |
| Flexibility | Low | Medium | High |

### 5.2 When to Use Which

| Scenario | Use |
|---|---|
| Wait for N parallel tasks to finish, one-time | `CountDownLatch` |
| Fixed group of threads, repeated phases | `CyclicBarrier` |
| Dynamic participants, complex phased coordination | `Phaser` |
| Two-thread bidirectional data swap | `Exchanger` |

---

## 6. Best Practices

### 6.1 Always Signal in a Finally Block

If a task fails and never calls `countDown()` or `arrive()`, other threads wait forever.

```java
try
{
    doWork();
}
catch (Exception e)
{
    log.error("Task failed", e);
}
finally
{
    latch.countDown(); // always release — even on failure
}
```

Same pattern applies to `Phaser`:

```java
try
{
    doWork();
}
finally
{
    phaser.arrive(); // always signal — do not leave others waiting
}
```

### 6.2 Handle BrokenBarrierException in CyclicBarrier

`BrokenBarrierException` is thrown when another party was interrupted or timed out, making the barrier unusable for that cycle. Always handle it and abort the current phase cleanly.

```java
try
{
    barrier.await();
}
catch (BrokenBarrierException e)
{
    // another thread interrupted or timed out — barrier is broken for this cycle
    log.error("Barrier broken, aborting phase");
    return;
}
catch (InterruptedException e)
{
    Thread.currentThread().interrupt();
}
```

### 6.3 Use Timeout on exchange() in Exchanger

If the second thread never arrives, `exchange()` blocks forever. Always use the timeout overload in production.

```java
try
{
    String result = exchanger.exchange(myData, 5, TimeUnit.SECONDS);
}
catch (TimeoutException e)
{
    // other thread did not arrive in time — handle or retry
    log.warn("Exchange timed out");
}
catch (InterruptedException e)
{
    Thread.currentThread().interrupt();
}
```