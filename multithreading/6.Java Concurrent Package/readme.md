# Java Concurrent Package - Production-Ready Tools

A practical guide to `java.util.concurrent` - the tools that professionals use daily for thread-safe applications.

---

## Table of Contents
1. [Introduction](#introduction)
2. [Thread Management](#thread-management)
3. [Locks & Synchronizers](#locks--synchronizers)
4. [Concurrent Collections](#concurrent-collections)
5. [Atomic Classes](#atomic-classes)
6. [Best Practices](#best-practices)
7. [Quick Reference](#quick-reference)

---

## Introduction

### Why `java.util.concurrent`?

Before this package, developers had to use low-level primitives like `synchronized`, `wait()`, and `notify()`. These are error-prone and hard to get right.

**Old way (error-prone):**
```java
synchronized (lock) {
    while (!condition) {
        lock.wait();
    }
    // Do work
}
```

**Modern way (easier, safer):**
```java
ExecutorService executor = Executors.newFixedThreadPool(10);
executor.submit(() -> doWork());
```

### The Three Pillars

1. **Thread Management**: Execute tasks without manually creating threads
2. **Synchronization**: Coordinate threads safely and efficiently
3. **Thread-Safe Collections**: Share data without explicit locking

---

## Thread Management

### The Problem with Manual Thread Creation

```java
// BAD: Creating threads manually
for (int i = 0; i < 1000; i++) {
    new Thread(() -> processTask()).start();  // Creates 1000 threads!
}
```

**Problems:**
- Thread creation is expensive
- Too many threads overwhelm the system
- No way to control or cancel tasks
- Resource leaks if not managed properly

### Solution: Executor Framework

The `Executor` framework manages a pool of worker threads for you.

---

### 1. Basic Executor Interface

```java
public interface Executor {
    void execute(Runnable command);
}
```

Simple interface: submit a task, let the executor decide how to run it.

---

### 2. ExecutorService

Enhanced executor with lifecycle management and task submission methods.

```java
ExecutorService executor = Executors.newFixedThreadPool(5);

// Submit a task
executor.submit(() -> {
    System.out.println("Task running in: " + Thread.currentThread().getName());
});

// Shutdown when done
executor.shutdown();
```

#### Important Methods

```java
// Submit task and get a Future
Future<String> future = executor.submit(() -> "Result");

// Submit multiple tasks
List<Callable<String>> tasks = Arrays.asList(task1, task2, task3);
List<Future<String>> results = executor.invokeAll(tasks);

// Shutdown gracefully
executor.shutdown();  // No new tasks accepted

// Force shutdown
executor.shutdownNow();  // Attempts to stop running tasks

// Wait for termination
executor.awaitTermination(1, TimeUnit.MINUTES);
```

---

### 3. Thread Pools

#### Fixed Thread Pool

Fixed number of threads. Best for CPU-bound tasks.

```java
ExecutorService executor = Executors.newFixedThreadPool(4);

for (int i = 0; i < 100; i++) {
    int taskId = i;
    executor.submit(() -> {
        System.out.println("Processing task " + taskId);
        // Only 4 tasks run simultaneously
    });
}

executor.shutdown();
```

**Use when:** You know the optimal thread count (usually CPU cores for CPU-bound work).

**Example:** Image processing, data analysis, calculations.

---

#### Cached Thread Pool

Creates threads as needed, reuses idle threads.

```java
ExecutorService executor = Executors.newCachedThreadPool();

// Handles bursts of tasks efficiently
for (int i = 0; i < 100; i++) {
    executor.submit(() -> {
        // Quick I/O task
        makeNetworkCall();
    });
}

executor.shutdown();
```

**Use when:** Many short-lived tasks, unpredictable workload.

**Example:** Handling HTTP requests, quick I/O operations.

**Warning:** Can create unlimited threads if tasks keep coming!

---

#### Single Thread Executor

Guarantees tasks execute sequentially in submission order.

```java
ExecutorService executor = Executors.newSingleThreadExecutor();

executor.submit(() -> writeToLog("Event 1"));
executor.submit(() -> writeToLog("Event 2"));
executor.submit(() -> writeToLog("Event 3"));
// Always executes in order: Event 1 → Event 2 → Event 3

executor.shutdown();
```

**Use when:** Tasks must execute in order, like logging or event processing.

**Example:** Log file writing, message queue consumer, sequential updates.

---

#### Scheduled Thread Pool

Execute tasks after a delay or periodically.

```java
ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(2);

// Run once after 5 seconds
scheduler.schedule(() -> {
    System.out.println("Delayed task");
}, 5, TimeUnit.SECONDS);

// Run every 10 seconds, starting after 1 second
scheduler.scheduleAtFixedRate(() -> {
    System.out.println("Periodic task: " + System.currentTimeMillis());
}, 1, 10, TimeUnit.SECONDS);

// Run 10 seconds after the previous execution completes
scheduler.scheduleWithFixedDelay(() -> {
    System.out.println("Task with delay between executions");
}, 1, 10, TimeUnit.SECONDS);
```

**Use when:** Periodic tasks, scheduled jobs, timeouts.

**Example:** Health checks, cache cleanup, scheduled reports, heartbeats.

---

### Real-World Example: Web Server Task Processing

```java
public class TaskProcessor {
    private final ExecutorService executor;
    
    public TaskProcessor() {
        // CPU cores for CPU-bound tasks
        int cores = Runtime.getRuntime().availableProcessors();
        this.executor = Executors.newFixedThreadPool(cores);
    }
    
    public void processUserRequests(List<Request> requests) {
        List<Future<Response>> futures = new ArrayList<>();
        
        for (Request request : requests) {
            Future<Response> future = executor.submit(() -> {
                return handleRequest(request);
            });
            futures.add(future);
        }
        
        // Collect results
        for (Future<Response> future : futures) {
            try {
                Response response = future.get(5, TimeUnit.SECONDS);
                sendResponse(response);
            } catch (TimeoutException e) {
                System.err.println("Request timed out");
            } catch (Exception e) {
                System.err.println("Request failed: " + e.getMessage());
            }
        }
    }
    
    public void shutdown() {
        executor.shutdown();
        try {
            if (!executor.awaitTermination(60, TimeUnit.SECONDS)) {
                executor.shutdownNow();
            }
        } catch (InterruptedException e) {
            executor.shutdownNow();
        }
    }
}
```

---

## Locks & Synchronizers

### Beyond `synchronized`

The `synchronized` keyword is simple but limited:
- Can't try to acquire lock without blocking
- Can't interrupt a thread waiting for a lock
- No separate read/write locks
- All-or-nothing locking

The Lock API solves these problems.

---

### 1. ReentrantLock

A more flexible alternative to `synchronized`.

#### Basic Usage

```java
private final ReentrantLock lock = new ReentrantLock();
private int count = 0;

public void increment() {
    lock.lock();
    try {
        count++;
    } finally {
        lock.unlock();  // ALWAYS unlock in finally!
    }
}
```

**Critical:** Always unlock in a `finally` block to prevent deadlocks!

#### Advanced Features

```java
// Try to acquire lock without blocking
if (lock.tryLock()) {
    try {
        // Got the lock
        updateSharedResource();
    } finally {
        lock.unlock();
    }
} else {
    // Didn't get lock, do something else
    System.out.println("Resource busy, try later");
}

// Try with timeout
if (lock.tryLock(2, TimeUnit.SECONDS)) {
    try {
        updateSharedResource();
    } finally {
        lock.unlock();
    }
} else {
    System.out.println("Timeout acquiring lock");
}

// Interruptible lock acquisition
try {
    lock.lockInterruptibly();
    try {
        longRunningTask();
    } finally {
        lock.unlock();
    }
} catch (InterruptedException e) {
    // Thread interrupted while waiting
}
```

#### Fair vs Unfair Locks

```java
// Unfair (default): Better performance, may starve threads
ReentrantLock unfairLock = new ReentrantLock();

// Fair: Threads acquire lock in order, prevents starvation
ReentrantLock fairLock = new ReentrantLock(true);
```

---

### 2. ReadWriteLock

Allows multiple readers OR one writer - perfect for read-heavy scenarios.

```java
public class CachedData {
    private final ReadWriteLock rwLock = new ReentrantReadWriteLock();
    private final Lock readLock = rwLock.readLock();
    private final Lock writeLock = rwLock.writeLock();
    private Map<String, String> cache = new HashMap<>();
    
    // Many threads can read simultaneously
    public String read(String key) {
        readLock.lock();
        try {
            return cache.get(key);
        } finally {
            readLock.unlock();
        }
    }
    
    // Only one thread can write
    public void write(String key, String value) {
        writeLock.lock();
        try {
            cache.put(key, value);
        } finally {
            writeLock.unlock();
        }
    }
}
```

**Use when:** Reads are much more frequent than writes (90%+ reads).

**Example:** Configuration cache, reference data, lookup tables.

---

### 3. Semaphore

Controls access to a resource with limited capacity.

```java
public class ConnectionPool {
    private final Semaphore available = new Semaphore(10);  // 10 permits
    private final List<Connection> connections;
    
    public Connection getConnection() throws InterruptedException {
        available.acquire();  // Block until permit available
        return getNextAvailableConnection();
    }
    
    public void returnConnection(Connection conn) {
        returnToPool(conn);
        available.release();  // Return the permit
    }
}
```

**Real-world usage:**

```java
// Limit concurrent downloads to 3
Semaphore downloadLimiter = new Semaphore(3);

for (String url : urls) {
    executor.submit(() -> {
        try {
            downloadLimiter.acquire();
            downloadFile(url);
        } finally {
            downloadLimiter.release();
        }
    });
}
```

**Use when:** Limiting access to expensive resources (connections, file handles, API calls).

---

### 4. CountDownLatch

Wait for N events to complete before proceeding. One-time use only.

```java
public class ServiceStarter {
    public void startServices() throws InterruptedException {
        int serviceCount = 3;
        CountDownLatch latch = new CountDownLatch(serviceCount);
        
        // Start services
        new Thread(() -> {
            initializeDatabase();
            latch.countDown();  // Signal completion
        }).start();
        
        new Thread(() -> {
            initializeCache();
            latch.countDown();
        }).start();
        
        new Thread(() -> {
            initializeMessageQueue();
            latch.countDown();
        }).start();
        
        // Wait for all services to start
        latch.await();  // Blocks until count reaches 0
        System.out.println("All services started!");
    }
}
```

**Use when:** Waiting for multiple tasks to complete before proceeding.

**Example:** Application initialization, waiting for worker threads, coordinated start.

---

### 5. CyclicBarrier

All threads wait at a barrier point, then proceed together. Reusable.

```java
public class ParallelProcessor {
    public void processInPhases(List<Data> dataList) {
        int threadCount = 4;
        CyclicBarrier barrier = new CyclicBarrier(threadCount, () -> {
            System.out.println("Phase complete! All threads synchronized.");
        });
        
        for (int i = 0; i < threadCount; i++) {
            new Thread(() -> {
                try {
                    // Phase 1
                    processPhase1();
                    barrier.await();  // Wait for all threads
                    
                    // Phase 2
                    processPhase2();
                    barrier.await();  // Wait again
                    
                    // Phase 3
                    processPhase3();
                    barrier.await();
                    
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }).start();
        }
    }
}
```

**Key difference from CountDownLatch:**
- CyclicBarrier is reusable (resets after all threads pass)
- CountDownLatch is one-time use

**Use when:** Multi-phase computations where all threads must sync at each phase.

**Example:** Parallel simulations, iterative algorithms, synchronized processing.

---

### 6. Phaser

Advanced synchronizer combining features of CountDownLatch and CyclicBarrier with dynamic parties.

```java
public class PhasedWorkflow {
    public void executeWorkflow() {
        Phaser phaser = new Phaser(1);  // 1 = main thread
        
        // Dynamically add parties (workers)
        for (int i = 0; i < 3; i++) {
            phaser.register();  // Add a party
            new Thread(() -> {
                try {
                    // Phase 0
                    System.out.println("Phase 0 work");
                    phaser.arriveAndAwaitAdvance();
                    
                    // Phase 1
                    System.out.println("Phase 1 work");
                    phaser.arriveAndAwaitAdvance();
                    
                    // Done - deregister
                    phaser.arriveAndDeregister();
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }).start();
        }
        
        // Main thread coordinates
        phaser.arriveAndAwaitAdvance();  // Wait for Phase 0
        System.out.println("All threads completed phase 0");
        
        phaser.arriveAndAwaitAdvance();  // Wait for Phase 1
        System.out.println("All threads completed phase 1");
        
        phaser.arriveAndDeregister();  // Main thread done
    }
}
```

**Use when:** You need dynamic number of parties or multiple reusable phases.

**Example:** Complex workflows, fork-join patterns, tiered processing.

---

### Synchronizer Comparison

| Tool | Reusable | Dynamic Parties | Use Case |
|------|----------|-----------------|----------|
| CountDownLatch | ❌ No | ❌ No | Wait for N events once |
| CyclicBarrier | ✅ Yes | ❌ No | Sync N threads repeatedly |
| Phaser | ✅ Yes | ✅ Yes | Complex multi-phase workflows |
| Semaphore | ✅ Yes | N/A | Limit concurrent access |

---

## Concurrent Collections

### The Problem with Regular Collections

```java
// UNSAFE with multiple threads!
Map<String, String> map = new HashMap<>();

// Thread 1
map.put("key1", "value1");

// Thread 2
map.put("key2", "value2");  // Can corrupt HashMap!
```

**Old solution:** Wrap with `Collections.synchronizedMap()` - but this locks the entire collection for every operation (slow!).

**Modern solution:** Use concurrent collections with fine-grained locking.

---

### 1. ConcurrentHashMap

Thread-safe HashMap with high concurrency.

#### Basic Usage

```java
ConcurrentHashMap<String, Integer> scores = new ConcurrentHashMap<>();

// Thread-safe operations
scores.put("Alice", 100);
scores.get("Alice");
scores.remove("Alice");
```

#### Atomic Compound Operations

```java
// Bad: Not atomic (race condition)
if (!map.containsKey("key")) {
    map.put("key", value);  // Another thread could insert between check and put
}

// Good: Atomic operation
map.putIfAbsent("key", value);

// Other atomic operations
map.computeIfAbsent("key", k -> expensiveComputation(k));
map.computeIfPresent("key", (k, v) -> v + 1);
map.merge("key", 1, Integer::sum);  // Atomic increment
```

#### Real-World Example: Request Counter

```java
public class RequestCounter {
    private final ConcurrentHashMap<String, AtomicInteger> counts = 
        new ConcurrentHashMap<>();
    
    public void recordRequest(String endpoint) {
        // Atomic: create counter if absent, then increment
        counts.computeIfAbsent(endpoint, k -> new AtomicInteger(0))
              .incrementAndGet();
    }
    
    public int getCount(String endpoint) {
        AtomicInteger counter = counts.get(endpoint);
        return counter == null ? 0 : counter.get();
    }
}
```

**Performance:** Multiple threads can read/write different segments simultaneously!

---

### 2. CopyOnWriteArrayList

Thread-safe list optimized for reads. Writes create a new copy.

```java
CopyOnWriteArrayList<String> listeners = new CopyOnWriteArrayList<>();

// Writes are expensive (copies entire array)
listeners.add("Listener1");
listeners.add("Listener2");

// Reads are super fast (no locking needed)
for (String listener : listeners) {
    notifyListener(listener);  // Safe even if another thread modifies list
}
```

**Use when:**
- Reads >>> Writes (90%+ reads)
- Small list size
- Iteration without locking needed

**Example:** Event listeners, observer lists, subscriber lists.

**Don't use when:** Frequent writes or large lists (copying is expensive!).

---

### 3. CopyOnWriteArraySet

Set version of CopyOnWriteArrayList with the same characteristics.

```java
CopyOnWriteArraySet<String> activeUsers = new CopyOnWriteArraySet<>();

activeUsers.add("user1");
activeUsers.remove("user2");

// Iteration never throws ConcurrentModificationException
for (String user : activeUsers) {
    sendNotification(user);
}
```

---

### 4. BlockingQueue

Thread-safe queue with blocking operations - perfect for producer-consumer patterns.

#### Common Implementations

**ArrayBlockingQueue**: Fixed capacity, array-based
```java
BlockingQueue<Task> queue = new ArrayBlockingQueue<>(100);  // Max 100 items
```

**LinkedBlockingQueue**: Optionally bounded, linked-node based
```java
BlockingQueue<Task> queue = new LinkedBlockingQueue<>();  // Unbounded
BlockingQueue<Task> queue = new LinkedBlockingQueue<>(1000);  // Bounded
```

**PriorityBlockingQueue**: Unbounded, ordered by priority
```java
BlockingQueue<Task> queue = new PriorityBlockingQueue<>();
```

**SynchronousQueue**: No capacity, direct handoff
```java
BlockingQueue<Task> queue = new SynchronousQueue<>();
```

#### Producer-Consumer Example

```java
public class TaskProcessingSystem {
    private final BlockingQueue<Task> taskQueue = new LinkedBlockingQueue<>(100);
    
    // Producer thread
    public void submitTask(Task task) throws InterruptedException {
        taskQueue.put(task);  // Blocks if queue is full
    }
    
    // Consumer thread
    public void processTask() throws InterruptedException {
        while (true) {
            Task task = taskQueue.take();  // Blocks if queue is empty
            process(task);
        }
    }
}
```

#### Key Methods

```java
// Blocking operations
queue.put(item);      // Wait if full
queue.take();         // Wait if empty

// Timeout operations
queue.offer(item, 1, TimeUnit.SECONDS);  // Wait up to 1 sec
queue.poll(1, TimeUnit.SECONDS);         // Wait up to 1 sec

// Non-blocking operations
queue.offer(item);    // Returns false if full
queue.poll();         // Returns null if empty
```

#### Real-World Example: Task Executor

```java
public class WorkerPool {
    private final BlockingQueue<Runnable> workQueue;
    private final List<Worker> workers;
    
    public WorkerPool(int numWorkers) {
        workQueue = new LinkedBlockingQueue<>();
        workers = new ArrayList<>();
        
        // Start worker threads
        for (int i = 0; i < numWorkers; i++) {
            Worker worker = new Worker(workQueue);
            worker.start();
            workers.add(worker);
        }
    }
    
    public void submit(Runnable task) throws InterruptedException {
        workQueue.put(task);
    }
    
    private static class Worker extends Thread {
        private final BlockingQueue<Runnable> queue;
        
        Worker(BlockingQueue<Runnable> queue) {
            this.queue = queue;
        }
        
        public void run() {
            try {
                while (true) {
                    Runnable task = queue.take();
                    task.run();
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }
    }
}
```

**Use when:** Producer-consumer scenarios, work queues, task pipelines.

---

### 5. ConcurrentLinkedQueue

Non-blocking, thread-safe queue using lock-free algorithms.

```java
ConcurrentLinkedQueue<String> queue = new ConcurrentLinkedQueue<>();

queue.offer("item1");
String item = queue.poll();  // Returns null if empty
```

**Use when:** High-throughput scenarios where blocking isn't desired.

---

### Collection Comparison

| Collection | Blocking | Bounded | Best For |
|------------|----------|---------|----------|
| ConcurrentHashMap | No | No | General key-value storage |
| CopyOnWriteArrayList | No | No | Read-heavy lists |
| ArrayBlockingQueue | Yes | Yes | Fixed-size work queues |
| LinkedBlockingQueue | Yes | Optional | General work queues |
| ConcurrentLinkedQueue | No | No | High-throughput queues |

---

## Atomic Classes

### The Problem

```java
private volatile int count = 0;

public void increment() {
    count++;  // NOT atomic! Race condition!
}
```

Even with `volatile`, compound operations aren't atomic.

### Solution: Atomic Variables

Provide lock-free, thread-safe atomic operations using CPU-level compare-and-swap (CAS).

---

### 1. AtomicInteger

```java
AtomicInteger counter = new AtomicInteger(0);

// Atomic operations
counter.incrementAndGet();  // ++count
counter.getAndIncrement();  // count++
counter.decrementAndGet();  // --count
counter.addAndGet(5);       // count += 5

// Get without modifying
int value = counter.get();

// Set new value
counter.set(100);

// Compare and swap
counter.compareAndSet(100, 200);  // If value is 100, set to 200
```

#### Real Example: ID Generator

```java
public class IdGenerator {
    private final AtomicInteger nextId = new AtomicInteger(1);
    
    public int generateId() {
        return nextId.getAndIncrement();  // Thread-safe, no locks!
    }
}
```

---

### 2. AtomicLong

Same as AtomicInteger but for `long` values.

```java
AtomicLong requestCount = new AtomicLong(0);

public void recordRequest() {
    requestCount.incrementAndGet();
}

public long getTotalRequests() {
    return requestCount.get();
}
```

**Use when:** Counters, timestamps, sequence numbers.

---

### 3. AtomicBoolean

```java
AtomicBoolean initialized = new AtomicBoolean(false);

public void initialize() {
    if (initialized.compareAndSet(false, true)) {
        // Only one thread executes this
        performInitialization();
    }
}
```

**Use when:** One-time initialization, flags.

---

### 4. AtomicReference

Thread-safe reference to an object.

```java
public class Configuration {
    private final AtomicReference<Settings> settings = 
        new AtomicReference<>(new Settings());
    
    public void updateSettings(Settings newSettings) {
        settings.set(newSettings);  // Atomic update
    }
    
    public Settings getSettings() {
        return settings.get();
    }
}
```

#### Compare-And-Set Pattern

```java
public class Stack<T> {
    private final AtomicReference<Node<T>> top = new AtomicReference<>();
    
    public void push(T value) {
        Node<T> newNode = new Node<>(value);
        Node<T> oldTop;
        do {
            oldTop = top.get();
            newNode.next = oldTop;
        } while (!top.compareAndSet(oldTop, newNode));  // Retry if failed
    }
}
```

---

### 5. AtomicStampedReference

Solves the ABA problem with versioning.

```java
// Problem: Value changes A → B → A (CAS can't detect this)
AtomicStampedReference<String> ref = 
    new AtomicStampedReference<>("initial", 0);

int[] stampHolder = new int[1];
String value = ref.get(stampHolder);
int stamp = stampHolder[0];

// Update with new stamp
ref.compareAndSet(value, "newValue", stamp, stamp + 1);
```

**Use when:** Detecting if a value changed even if it changed back.

---

### Performance: Atomic vs Synchronized

```java
// Atomic: Lock-free, faster for low contention
private AtomicInteger count = new AtomicInteger(0);
public void increment() {
    count.incrementAndGet();
}

// Synchronized: Uses locks, better for high contention
private int count = 0;
public synchronized void increment() {
    count++;
}
```

**Rule of thumb:** Use atomic classes for simple counters and flags. Use locks for complex operations.

---

## Best Practices

### 1. Always Shutdown Executors

```java
ExecutorService executor = Executors.newFixedThreadPool(10);
try {
    // Use executor
} finally {
    executor.shutdown();
    try {
        if (!executor.awaitTermination(60, TimeUnit.SECONDS)) {
            executor.shutdownNow();
        }
    } catch (InterruptedException e) {
        executor.shutdownNow();
    }
}
```

### 2. Use try-finally with Locks

```java
Lock lock = new ReentrantLock();
lock.lock();
try {
    // Critical section
} finally {
    lock.unlock();  // ALWAYS in finally
}
```

### 3. Choose the Right Pool Type

```java
// CPU-bound tasks: Fixed pool with #cores threads
int cores = Runtime.getRuntime().availableProcessors();
ExecutorService cpuPool = Executors.newFixedThreadPool(cores);

// I/O-bound tasks: Cached pool or larger fixed pool
ExecutorService ioPool = Executors.newCachedThreadPool();
```

### 4. Use Concurrent Collections

```java
// Bad
Map<String, String> map = Collections.synchronizedMap(new HashMap<>());

// Good
Map<String, String> map = new ConcurrentHashMap<>();
```

### 5. Prefer High-Level Tools

```java
// Bad: Manual thread management
new Thread(() -> doWork()).start();

// Good: Use executor
executor.submit(() -> doWork());
```

### 6. Handle InterruptedException Properly

```java
try {
    queue.take();
} catch (InterruptedException e) {
    Thread.currentThread().interrupt();  // Restore interrupt status
    // Handle interruption
}
```

### 7. Use Atomic Classes for Simple Operations

```java
// Bad: synchronized for simple counter
private int count = 0;
public synchronized void increment() {
    count++;
}

// Good: atomic class
private AtomicInteger count = new AtomicInteger(0);
public void increment() {
    count.incrementAndGet();
}
```

---

## Quick Reference

### Thread Pools Decision Tree

```
What kind of tasks?
    │
    ├─ CPU-bound (calculations) → newFixedThreadPool(#cores)
    │
    ├─ I/O-bound (network, disk) → newCachedThreadPool()
    │
    ├─ Sequential execution needed → newSingleThreadExecutor()
    │
    └─ Scheduled/periodic tasks → newScheduledThreadPool()
```

### Synchronizer Decision Tree

```
What do you need?
    │
    ├─ Wait for N events once → CountDownLatch
    │
    ├─ Sync N threads repeatedly → CyclicBarrier
    │
    ├─ Limit concurrent access → Semaphore
    │
    ├─ Complex multi-phase workflow → Phaser
    │
    └─ Fine-grained control over locking → ReentrantLock
```

### Collection Decision Tree

```
What's your access pattern?
    │
    ├─ Key-value storage → ConcurrentHashMap
    │
    ├─ List with rare writes → CopyOnWriteArrayList
    │
    ├─ Producer-consumer queue → BlockingQueue
    │   ├─ Fixed size → ArrayBlockingQueue
    │   └─ Dynamic size → LinkedBlockingQueue
    │
    └─ High-throughput queue → ConcurrentLinkedQueue
```

### When to Use What

| Need | Use |
|------|-----|
| Thread-safe counter | AtomicInteger |
| Thread-safe flag | AtomicBoolean |
| Thread-safe reference | AtomicReference |
| Execute tasks | ExecutorService |
| Limit concurrent access | Semaphore |
| Wait for events | CountDownLatch |
| Sync threads in phases | CyclicBarrier |
| Thread-safe map | ConcurrentHashMap |
| Producer-consumer | BlockingQueue |
| Read-heavy list | CopyOnWriteArrayList |

---

## Common Pitfalls

### 1. Forgetting to Shutdown Executors

```java
// Bad: Executor never shuts down (app won't exit!)
ExecutorService executor = Executors.newFixedThreadPool(10);
executor.submit(() -> doWork());
// Missing: executor.shutdown()
```

### 2. Not Using finally for Locks

```java
// Bad: Lock never released if exception occurs
lock.lock();
doWork();  // If this throws, lock is never released!
lock.unlock();

// Good
lock.lock();
try {
    doWork();
} finally {
    lock.unlock();
}
```

### 3. Using volatile for Compound Operations

```java
// Bad: Race condition
private volatile int count = 0;
public void increment() {
    count++;  // Not atomic!
}

// Good
private AtomicInteger count = new AtomicInteger(0);
public void increment() {
    count.incrementAndGet();
}
```

### 4. Blocking Queue Deadlock

```java
// Bad: Can deadlock with bounded queue
BlockingQueue<Task> queue = new ArrayBlockingQueue<>(1);
queue.put(task1);  // Blocks if full
queue.put(task2);  // Deadlock if no consumer!

// Good: Handle interruption and timeouts
if (!queue.offer(task, 1, TimeUnit.SECONDS)) {
    // Handle failure
}
```

---

## Summary

The `java.util.concurrent` package provides production-ready tools that are:
- **Safer**: Built-in thread safety
- **Faster**: Optimized for concurrency
- **Easier**: Higher-level abstractions

**Key takeaway:** Don't reinvent the wheel. Use these battle-tested tools instead of low-level primitives like `synchronized`, `wait()`, and `notify()`.

### The Professional Approach

1. **Start with ExecutorService** instead of creating threads manually
2. **Use ConcurrentHashMap** instead of synchronized collections
3. **Use AtomicInteger** instead of synchronized counters
4. **Use BlockingQueue** for producer-consumer patterns
5. **Use CountDownLatch/CyclicBarrier** instead of complex wait/notify logic

These tools are what separate junior developers from professionals who write production-grade concurrent applications.

---

## Further Reading

- Java Concurrency in Practice by Brian Goetz
- Oracle Java Concurrency Tutorial
- javadoc for `java.util.concurrent` package
