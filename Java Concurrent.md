
These are some of the most important Java concurrency utilities. They solve different synchronization problems that `synchronized` alone cannot solve elegantly.

---

# 1. Semaphore

A **Semaphore** controls how many threads can access a resource at the same time.

Think of it as a parking lot with only 3 parking spaces.

---

## Example: Only 2 Threads Can Access Resource

```java
import java.util.concurrent.Semaphore;

public class SemaphoreExample {

    private static final Semaphore semaphore = new Semaphore(2);

    public static void main(String[] args) {

        for (int i = 1; i <= 5; i++) {

            int id = i;

            new Thread(() -> {

                try {
                    semaphore.acquire();

                    System.out.println("Thread " + id + " acquired permit");

                    Thread.sleep(3000);

                    System.out.println("Thread " + id + " releasing permit");

                    semaphore.release();

                } catch (Exception e) {
                    e.printStackTrace();
                }

            }).start();
        }
    }
}
```

### Output

```text
Thread 1 acquired permit
Thread 2 acquired permit

(wait...)

Thread 1 releasing permit
Thread 3 acquired permit

Thread 2 releasing permit
Thread 4 acquired permit
```

### Real Use Cases

- Database connection pool
    
- API rate limiting
    
- Limiting concurrent downloads
    
- Printer access
    

---

# 2. CountDownLatch

Used when one thread must wait until other threads finish.

It is a **one-time-use synchronization aid**.

---

## Example: Wait For 3 Services To Start

```java
import java.util.concurrent.CountDownLatch;

public class CountDownLatchExample {

    public static void main(String[] args) throws Exception {

        CountDownLatch latch = new CountDownLatch(3);

        Runnable service = () -> {
            try {
                Thread.sleep(2000);
                System.out.println(Thread.currentThread().getName() + " started");
                latch.countDown();
            } catch (Exception e) {
            }
        };

        new Thread(service, "DB").start();
        new Thread(service, "Cache").start();
        new Thread(service, "MessageQueue").start();

        latch.await();

        System.out.println("All services are up");
    }
}
```

### Output

```text
DB started
Cache started
MessageQueue started

All services are up
```

### Real Use Cases

- Application startup
    
- Wait for all tasks to complete
    
- Parallel processing
    

---

# 3. CyclicBarrier

Multiple threads wait for each other before proceeding.

Unlike CountDownLatch, it is **reusable**.

---

## Example: 3 Players Wait Before Game Starts

```java
import java.util.concurrent.CyclicBarrier;

public class CyclicBarrierExample {

    public static void main(String[] args) {

        CyclicBarrier barrier =
                new CyclicBarrier(3,
                        () -> System.out.println("Game Started"));

        Runnable player = () -> {

            try {

                System.out.println(
                        Thread.currentThread().getName() + " ready");

                barrier.await();

                System.out.println(
                        Thread.currentThread().getName() + " playing");

            } catch (Exception e) {
                e.printStackTrace();
            }
        };

        new Thread(player, "Player1").start();
        new Thread(player, "Player2").start();
        new Thread(player, "Player3").start();
    }
}
```

### Output

```text
Player1 ready
Player2 ready
Player3 ready

Game Started

Player1 playing
Player2 playing
Player3 playing
```

---

## CountDownLatch vs CyclicBarrier

|Feature|CountDownLatch|CyclicBarrier|
|---|---|---|
|Reusable|❌ No|✅ Yes|
|Waiting Threads|One or more wait|All wait|
|Counter Reset|❌ No|✅ Yes|
|Use Case|Startup completion|Phase synchronization|

---

# 4. ExecutorService

Manages thread pools.

Instead of:

```java
new Thread(task).start();
```

use a pool of reusable threads.

---

## Fixed Thread Pool

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class ExecutorExample {

    public static void main(String[] args) {

        ExecutorService executor =
                Executors.newFixedThreadPool(2);

        for(int i=1;i<=5;i++){

            int id=i;

            executor.submit(() -> {

                System.out.println(
                        "Task " + id +
                        " executed by " +
                        Thread.currentThread().getName());

            });
        }

        executor.shutdown();
    }
}
```

### Output

```text
Task 1 executed by pool-1-thread-1
Task 2 executed by pool-1-thread-2
Task 3 executed by pool-1-thread-1
Task 4 executed by pool-1-thread-2
```

---

## Common Executors

### Fixed Pool

```java
Executors.newFixedThreadPool(10);
```

Fixed number of threads.

---

### Cached Pool

```java
Executors.newCachedThreadPool();
```

Creates threads as needed.

---

### Single Thread

```java
Executors.newSingleThreadExecutor();
```

Sequential execution.

---

### Scheduled Executor

```java
Executors.newScheduledThreadPool(2);
```

Runs delayed tasks.

---

# 5. ThreadFactory

Controls how threads are created.

Useful for naming threads.

---

## Custom ThreadFactory

```java
import java.util.concurrent.ThreadFactory;

public class CustomThreadFactory
        implements ThreadFactory {

    private int count = 1;

    @Override
    public Thread newThread(Runnable r) {

        Thread t = new Thread(r);

        t.setName("Worker-" + count++);

        return t;
    }
}
```

Usage:

```java
ExecutorService executor =
        Executors.newFixedThreadPool(
                2,
                new CustomThreadFactory());

executor.submit(() ->
        System.out.println(
                Thread.currentThread().getName()));
```

Output:

```text
Worker-1
```

---

## Real Use Cases

- Naming threads
    
- Daemon threads
    
- Uncaught exception handlers
    
- Logging
    

---

# 6. ThreadGroup

Logical grouping of threads.

Before ExecutorService became popular, ThreadGroup was used heavily.

---

## Example

```java
public class ThreadGroupExample {

    public static void main(String[] args) {

        ThreadGroup group =
                new ThreadGroup("Workers");

        Runnable task = () -> {

            System.out.println(
                    Thread.currentThread().getName());
        };

        new Thread(group, task, "T1").start();
        new Thread(group, task, "T2").start();

        System.out.println(
                "Active Threads = " +
                group.activeCount());
    }
}
```

### Output

```text
T1
T2
Active Threads = 2
```

---

## Why Rarely Used Today?

Modern applications use:

```java
ExecutorService
ForkJoinPool
CompletableFuture
Virtual Threads (Java 21+)
```

instead of ThreadGroup.

---

# 7. ReentrantLock

More powerful version of `synchronized`.

Provides:

- tryLock()
    
- lockInterruptibly()
    
- fairness
    
- multiple Conditions
    

---

## Basic Example

```java
import java.util.concurrent.locks.ReentrantLock;

public class Counter {

    private int count = 0;

    private ReentrantLock lock =
            new ReentrantLock();

    public void increment() {

        lock.lock();

        try {
            count++;
        } finally {
            lock.unlock();
        }
    }
}
```

---

## tryLock()

Avoid waiting forever.

```java
if(lock.tryLock()) {

    try {
        System.out.println("Lock acquired");
    } finally {
        lock.unlock();
    }

} else {

    System.out.println("Could not acquire lock");
}
```

---

## Fair Lock

```java
ReentrantLock lock =
        new ReentrantLock(true);
```

Threads acquire lock in arrival order.

---

## Reentrant Property

Same thread can acquire lock multiple times.

```java
lock.lock();

try {

    methodA();

} finally {
    lock.unlock();
}
```

```java
void methodA() {

    lock.lock();

    try {

        methodB();

    } finally {

        lock.unlock();
    }
}
```

No deadlock occurs because the same thread re-enters the lock.

---

# Quick Interview Summary

|Utility|Purpose|
|---|---|
|Semaphore|Limit concurrent access to N threads|
|CountDownLatch|Wait until N operations finish|
|CyclicBarrier|All threads wait for each other|
|ExecutorService|Thread pool management|
|ThreadFactory|Custom thread creation|
|ThreadGroup|Group threads logically|
|ReentrantLock|Advanced locking beyond synchronized|

### Banking System Examples (Lloyds/JPM/DBS style)

- **Semaphore** → Limit max 50 concurrent payment requests to a downstream service.
    
- **CountDownLatch** → Wait for Account Service, Customer Service, and Fraud Service during startup.
    
- **CyclicBarrier** → Parallel risk calculations where all threads must finish phase-1 before phase-2.
    
- **ExecutorService** → Process transaction events using a fixed thread pool.
    
- **ThreadFactory** → Create threads named `PAYMENT-WORKER-*` for easier debugging.
    
- **ThreadGroup** → Rarely used in modern enterprise applications.
    
- **ReentrantLock** → Protect account balance updates with `tryLock()` to avoid contention.



# `Condition` is one of the most powerful features of `ReentrantLock`.

If `Object.wait()` and `Object.notify()` belong to `synchronized`, then `Condition.await()` and `Condition.signal()` belong to `ReentrantLock`.

---

# Why Condition?

With `synchronized`, you get only **one waiting queue per object**.

```java
synchronized(lock) {
    lock.wait();
}
```

All waiting threads are put into the same queue.

---

With `ReentrantLock`, you can create **multiple waiting queues**.

```java
ReentrantLock lock = new ReentrantLock();

Condition notEmpty = lock.newCondition();
Condition notFull = lock.newCondition();
```

Now some threads can wait on `notEmpty` and others on `notFull`.

This is extremely useful in Producer-Consumer systems.

---

# Basic APIs

```java
Condition condition = lock.newCondition();
```

## Wait

```java
condition.await();
```

Current thread:

1. Releases lock
    
2. Goes into waiting state
    
3. Sleeps until signaled
    
4. Re-acquires lock
    
5. Continues execution
    

---

## Wake One Thread

```java
condition.signal();
```

Equivalent of:

```java
notify();
```

---

## Wake All Threads

```java
condition.signalAll();
```

Equivalent of:

```java
notifyAll();
```

---

# Example 1: Simple Await and Signal

Thread A waits.

Thread B wakes it.

```java
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.ReentrantLock;

public class ConditionDemo {

    private static final ReentrantLock lock =
            new ReentrantLock();

    private static final Condition condition =
            lock.newCondition();

    public static void main(String[] args) {

        Thread waiter = new Thread(() -> {

            lock.lock();

            try {

                System.out.println("Waiting...");

                condition.await();

                System.out.println("Resumed");

            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                lock.unlock();
            }

        });

        Thread signaler = new Thread(() -> {

            try {
                Thread.sleep(3000);
            } catch (Exception e) {
            }

            lock.lock();

            try {

                System.out.println("Sending signal");

                condition.signal();

            } finally {
                lock.unlock();
            }
        });

        waiter.start();
        signaler.start();
    }
}
```

Output

```text
Waiting...

(after 3 sec)

Sending signal
Resumed
```

---

# Producer Consumer Example

This is the most important interview example.

---

## Problem

Buffer size = 3

Producer:

- Wait if buffer full
    

Consumer:

- Wait if buffer empty
    

---

## Solution Using Two Conditions

```java
import java.util.*;
import java.util.concurrent.locks.*;

public class ProducerConsumer {

    private final Queue<Integer> queue =
            new LinkedList<>();

    private final int capacity = 3;

    private final ReentrantLock lock =
            new ReentrantLock();

    private final Condition notFull =
            lock.newCondition();

    private final Condition notEmpty =
            lock.newCondition();

    public void produce(int value)
            throws InterruptedException {

        lock.lock();

        try {

            while (queue.size() == capacity) {

                System.out.println("Buffer Full");

                notFull.await();
            }

            queue.offer(value);

            System.out.println(
                    "Produced: " + value);

            notEmpty.signal();

        } finally {
            lock.unlock();
        }
    }

    public void consume()
            throws InterruptedException {

        lock.lock();

        try {

            while (queue.isEmpty()) {

                System.out.println("Buffer Empty");

                notEmpty.await();
            }

            int value = queue.poll();

            System.out.println(
                    "Consumed: " + value);

            notFull.signal();

        } finally {
            lock.unlock();
        }
    }

    public static void main(String[] args) {

        ProducerConsumer pc =
                new ProducerConsumer();

        new Thread(() -> {

            int i = 1;

            while (true) {

                try {

                    pc.produce(i++);

                    Thread.sleep(1000);

                } catch (Exception e) {
                }
            }

        }).start();

        new Thread(() -> {

            while (true) {

                try {

                    pc.consume();

                    Thread.sleep(2000);

                } catch (Exception e) {
                }
            }

        }).start();
    }
}
```

---

# Why `while` and not `if`?

Never do:

```java
if(queue.isEmpty()) {
    notEmpty.await();
}
```

Always:

```java
while(queue.isEmpty()) {
    notEmpty.await();
}
```

Reason:

### Spurious Wakeup

A thread may wake up without a signal.

Java documentation explicitly warns about this.

Therefore:

```java
while(condition_not_satisfied) {
    await();
}
```

is the standard pattern.

---

# await() Variants

## Wait Forever

```java
condition.await();
```

---

## Wait With Timeout

```java
condition.await(
        5,
        TimeUnit.SECONDS);
```

Example:

```java
if(condition.await(5, TimeUnit.SECONDS)) {

    System.out.println("Signal received");

} else {

    System.out.println("Timed out");
}
```

---

## Wait Until Specific Time

```java
Date date =
        new Date(System.currentTimeMillis() + 5000);

condition.awaitUntil(date);
```

---

# signal() vs signalAll()

## signal()

Wakes one thread.

```java
condition.signal();
```

Use when only one waiter needs to proceed.

---

## signalAll()

Wakes every waiting thread.

```java
condition.signalAll();
```

Useful when:

```java
Application Started
Database Connected
Configuration Loaded
```

and all waiting workers can continue.

---

# Condition vs wait/notify

|Feature|wait/notify|Condition|
|---|---|---|
|Requires synchronized|Yes|No|
|Requires ReentrantLock|No|Yes|
|Multiple wait queues|No|Yes|
|Timeout support|Limited|Rich|
|Better readability|No|Yes|
|Enterprise usage|Rare|Common|

---

# Real Banking Example

Suppose you are processing payments.

You have:

```java
Condition fundsAvailable;
Condition fraudCheckCompleted;
Condition settlementCompleted;
```

A payment thread may:

```java
fundsAvailable.await();
fraudCheckCompleted.await();
settlementCompleted.await();
```

Different events wake different groups of waiting threads.

This is much cleaner than having a single `wait()` queue where every thread wakes up and checks whether the event it cares about has happened.

### Interview Answer

A `Condition` is a synchronization mechanism associated with a `ReentrantLock` that allows threads to wait (`await`) and be notified (`signal`/`signalAll`). Unlike `wait/notify`, a single lock can have multiple independent waiting queues, making it ideal for producer-consumer systems, workflow coordination, and complex thread synchronization.



