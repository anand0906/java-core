# Advanced Concurrency Patterns in Java

A deep dive into advanced patterns that separate expert concurrent programmers from the rest.

---

## Table of Contents
1. [Future & Callable](#future--callable)
2. [CompletableFuture](#completablefuture)
3. [Fork/Join Framework](#forkjoin-framework)
4. [Work Stealing](#work-stealing)
5. [Parallel Streams](#parallel-streams)
6. [Immutable Objects in Concurrency](#immutable-objects-in-concurrency)
7. [Thread Confinement](#thread-confinement)
8. [Double-Checked Locking](#double-checked-locking)
9. [Summary](#summary)

---

## Future & Callable

### The Problem with Runnable

`Runnable` has limitations:
- Can't return a value
- Can't throw checked exceptions

```java
Runnable task = () -> {
    // Can't return anything!
    int result = compute();  // Result is lost
};
```

### Enter Callable

`Callable` is like `Runnable` but better:

```java
Callable<Integer> task = () -> {
    return expensiveComputation();  // Can return a value!
};
```

**Key differences:**

| Feature | Runnable | Callable |
|---------|----------|----------|
| Return value | ❌ No (void) | ✅ Yes (generic type) |
| Checked exceptions | ❌ Can't throw | ✅ Can throw |
| Method name | run() | call() |

---

### Future: The Promise of a Result

A `Future` represents a result that will be available in the future.

**Think of it like ordering food:**
- You place an order (submit task)
- Get a receipt (Future)
- Receipt lets you check if food is ready
- When ready, you get your food (result)

```java
ExecutorService executor = Executors.newFixedThreadPool(2);

// Submit task, get Future immediately
Future<Integer> future = executor.submit(() -> {
    Thread.sleep(2000);  // Simulate long computation
    return 42;
});

// Do other work while task runs
doSomethingElse();

// Get result (blocks if not ready yet)
Integer result = future.get();  // Waits up to 2 seconds
System.out.println("Result: " + result);
```

### Future Operations

```java
// Check if done (non-blocking)
if (future.isDone()) {
    Integer result = future.get();
}

// Check if cancelled
if (future.isCancelled()) {
    System.out.println("Task was cancelled");
}

// Cancel the task
future.cancel(true);  // true = interrupt if running

// Get with timeout
try {
    Integer result = future.get(5, TimeUnit.SECONDS);
} catch (TimeoutException e) {
    System.out.println("Task took too long");
}
```

### Real-World Example: Multiple API Calls

```java
ExecutorService executor = Executors.newFixedThreadPool(3);

// Submit multiple tasks in parallel
Future<UserData> userFuture = executor.submit(() -> fetchUserData(userId));
Future<List<Order>> ordersFuture = executor.submit(() -> fetchOrders(userId));
Future<Settings> settingsFuture = executor.submit(() -> fetchSettings(userId));

// Collect results (waits for all to complete)
UserData user = userFuture.get();
List<Order> orders = ordersFuture.get();
Settings settings = settingsFuture.get();

// Now we have all data
displayDashboard(user, orders, settings);
```

### Limitations of Future

1. **Can't chain operations**: No way to say "when done, do this next"
2. **No combining**: Can't easily combine multiple Futures
3. **Blocking**: `get()` blocks the thread
4. **No completion callbacks**: Can't register "when complete, call this"

**Solution:** `CompletableFuture`

---

## CompletableFuture

### The Evolution

**Traditional Future:** "Tell me when you're done (blocking)"

**CompletableFuture:** "When you're done, automatically do the next thing (non-blocking)"

### Basic Concept

Think of it as a **pipeline** of asynchronous operations:

```
Start → Step 1 → Step 2 → Step 3 → Result
(async)   (async)   (async)   (async)
```

Each step starts automatically when the previous one completes.

---

### Creating CompletableFuture

```java
// Option 1: From a value (already completed)
CompletableFuture<String> future = CompletableFuture.completedFuture("Hello");

// Option 2: Run async (no return value)
CompletableFuture<Void> future = CompletableFuture.runAsync(() -> {
    System.out.println("Running in: " + Thread.currentThread().getName());
});

// Option 3: Supply async (with return value)
CompletableFuture<Integer> future = CompletableFuture.supplyAsync(() -> {
    return expensiveComputation();
});
```

---

### Chaining Operations

#### thenApply - Transform the result

```java
CompletableFuture<Integer> future = CompletableFuture
    .supplyAsync(() -> 5)           // Returns 5
    .thenApply(n -> n * 2)          // Returns 10
    .thenApply(n -> n + 3);         // Returns 13

System.out.println(future.get());   // 13
```

**Think:** Like `map()` in streams, transforms one value to another.

#### thenAccept - Consume the result

```java
CompletableFuture.supplyAsync(() -> fetchUserData())
    .thenAccept(user -> {
        System.out.println("User: " + user.getName());
        // No return value
    });
```

**Think:** Terminal operation, does something with the result.

#### thenRun - Do something after completion

```java
CompletableFuture.supplyAsync(() -> processData())
    .thenRun(() -> {
        System.out.println("Processing complete!");
        // Doesn't receive the result
    });
```

---

### Combining Multiple Futures

#### thenCombine - Combine two independent futures

```java
CompletableFuture<Integer> future1 = CompletableFuture.supplyAsync(() -> 10);
CompletableFuture<Integer> future2 = CompletableFuture.supplyAsync(() -> 20);

CompletableFuture<Integer> combined = future1.thenCombine(future2, (a, b) -> a + b);

System.out.println(combined.get());  // 30
```

**Runs both in parallel, then combines results.**

#### thenCompose - Chain dependent futures

```java
CompletableFuture<User> userFuture = CompletableFuture
    .supplyAsync(() -> fetchUserId())
    .thenCompose(userId -> fetchUserDetails(userId));  // Uses result of previous
```

**Think:** Like `flatMap()` in streams, one future depends on another's result.

#### allOf - Wait for multiple futures

```java
CompletableFuture<String> f1 = CompletableFuture.supplyAsync(() -> "Result1");
CompletableFuture<String> f2 = CompletableFuture.supplyAsync(() -> "Result2");
CompletableFuture<String> f3 = CompletableFuture.supplyAsync(() -> "Result3");

CompletableFuture<Void> all = CompletableFuture.allOf(f1, f2, f3);

all.join();  // Wait for all to complete
```

#### anyOf - Wait for first to complete

```java
CompletableFuture<String> fastest = (CompletableFuture<String>) 
    CompletableFuture.anyOf(slowService(), fastService(), mediumService());

String result = fastest.get();  // Gets result from fastest
```

---

### Error Handling

```java
CompletableFuture.supplyAsync(() -> {
    if (Math.random() > 0.5) {
        throw new RuntimeException("Failed!");
    }
    return "Success";
})
.exceptionally(ex -> {
    System.out.println("Error: " + ex.getMessage());
    return "Default Value";  // Fallback
})
.thenAccept(result -> System.out.println(result));
```

#### handle - Handle both success and failure

```java
future.handle((result, exception) -> {
    if (exception != null) {
        return "Error: " + exception.getMessage();
    }
    return "Success: " + result;
});
```

---

### Real-World Example: Async Pipeline

```java
public CompletableFuture<DashboardData> loadDashboard(String userId) {
    return CompletableFuture
        .supplyAsync(() -> validateUser(userId))      // Step 1: Validate
        .thenCompose(user -> fetchUserData(user))     // Step 2: Fetch data
        .thenCombine(
            fetchRecentActivity(userId),              // Step 3a: Parallel fetch
            (userData, activity) -> new DashboardData(userData, activity)
        )
        .thenApply(data -> enrichWithMetrics(data))   // Step 4: Enrich
        .exceptionally(ex -> createErrorDashboard(ex)); // Error handling
}
```

**What happens:**
1. Validates user (async)
2. When done, fetches user data (async)
3. While fetching user data, also fetches activity (parallel)
4. Combines both results
5. Enriches with metrics
6. If any step fails, shows error dashboard

**Key point:** No blocking! Each step starts automatically when ready.

---

### CompletableFuture vs Future

| Feature | Future | CompletableFuture |
|---------|--------|-------------------|
| Chaining | ❌ No | ✅ Yes |
| Combining | ❌ No | ✅ Yes |
| Callbacks | ❌ No | ✅ Yes |
| Exception handling | ❌ Manual | ✅ Built-in |
| Manual completion | ❌ No | ✅ Yes |

---

## Fork/Join Framework

### The Core Idea

**Problem:** You have a huge task that can be broken into smaller pieces.

**Solution:** Divide-and-conquer with work stealing.

```
Big Task
    ├── Subtask 1
    │   ├── Sub-subtask 1a
    │   └── Sub-subtask 1b
    └── Subtask 2
        ├── Sub-subtask 2a
        └── Sub-subtask 2b
```

### The Algorithm Pattern

```
if (task is small enough) {
    solve directly
} else {
    split into subtasks
    fork subtasks (execute in parallel)
    join results (wait and combine)
}
```

---

### RecursiveTask vs RecursiveAction

**RecursiveTask:** Returns a result
```java
class SumTask extends RecursiveTask<Long> {
    protected Long compute() {
        // Returns a sum
    }
}
```

**RecursiveAction:** No result (void)
```java
class ProcessTask extends RecursiveAction {
    protected void compute() {
        // Just does work
    }
}
```

---

### Simple Example: Array Sum

**Sequential approach:**
```
Sum [1,2,3,4,5,6,7,8] = 36
```

**Fork/Join approach:**
```
                [1,2,3,4,5,6,7,8]
                      ↓ fork
        [1,2,3,4]            [5,6,7,8]
           ↓ fork               ↓ fork
    [1,2]    [3,4]       [5,6]    [7,8]
      ↓        ↓           ↓        ↓
     3  +     7    +      11  +    15  = 36
```

**Code:**
```java
class SumTask extends RecursiveTask<Long> {
    private final int[] array;
    private final int start, end;
    private static final int THRESHOLD = 10;
    
    protected Long compute() {
        if (end - start <= THRESHOLD) {
            // Small enough: compute directly
            long sum = 0;
            for (int i = start; i < end; i++) {
                sum += array[i];
            }
            return sum;
        } else {
            // Split in half
            int mid = (start + end) / 2;
            SumTask left = new SumTask(array, start, mid);
            SumTask right = new SumTask(array, mid, end);
            
            left.fork();   // Execute left in parallel
            long rightResult = right.compute();  // Compute right here
            long leftResult = left.join();       // Wait for left
            
            return leftResult + rightResult;
        }
    }
}
```

**Key insight:** Each task decides whether to split or compute directly.

---

### The Threshold Decision

```java
private static final int THRESHOLD = 1000;
```

**Too small:** Too much overhead splitting tasks
**Too large:** Not enough parallelism

**Rule of thumb:** Each task should take at least a few milliseconds.

---

### Using ForkJoinPool

```java
ForkJoinPool pool = new ForkJoinPool();  // Uses all CPU cores
SumTask task = new SumTask(array, 0, array.length);
Long result = pool.invoke(task);
```

**Or use the common pool:**
```java
Long result = ForkJoinPool.commonPool().invoke(task);
```

---

### When to Use Fork/Join

✅ **Good for:**
- Large arrays/collections to process
- Recursive algorithms (merge sort, quicksort)
- Tree/graph traversal
- Tasks that split evenly

❌ **Bad for:**
- I/O operations (blocking)
- Tasks with uneven split sizes
- Too fine-grained tasks (overhead > benefit)

---

## Work Stealing

### The Problem

Traditional thread pool: Each thread has its own queue.

```
Thread 1: [Task1, Task2, Task3, Task4] ← Busy
Thread 2: [Task5] ← Idle
Thread 3: [] ← Idle
```

Thread 1 is overloaded while others are idle!

---

### The Solution: Work Stealing

Idle threads "steal" work from busy threads.

```
Before:
Thread 1: [T1, T2, T3, T4]
Thread 2: []

After stealing:
Thread 1: [T1, T2]
Thread 2: [T3, T4] ← Stolen from Thread 1
```

---

### How It Works

Each thread has a **deque** (double-ended queue):
- Owner takes from **head** (LIFO - last in, first out)
- Thieves steal from **tail** (FIFO - first in, first out)

```
Deque for Thread 1:
HEAD [T4, T3, T2, T1] TAIL
  ↑                    ↑
Owner pops here    Thieves steal here
```

**Why this design?**
- Owner and thief work on opposite ends → less contention
- Owner gets newest tasks (better cache locality)
- Thieves get oldest tasks (likely to spawn more work)

---

### Fork/Join Uses Work Stealing

When you call `fork()`:
```java
leftTask.fork();  // Adds to current thread's deque
```

Idle threads steal these forked tasks automatically!

---

### Benefits

1. **Load balancing**: No idle threads while others are busy
2. **Cache efficiency**: Threads work on related tasks
3. **Low contention**: Stealing is rare (only when unbalanced)

---

### Work Stealing in Action

```java
// ForkJoinPool uses work stealing by default
ForkJoinPool pool = new ForkJoinPool(4);  // 4 worker threads

// Thread 1 forks many tasks
task.fork();  // Added to Thread 1's queue
task.fork();  // Added to Thread 1's queue
task.fork();  // Added to Thread 1's queue

// Threads 2, 3, 4 are idle → they steal from Thread 1
```

**Automatic load balancing!**

---

## Parallel Streams

### The Promise

"Easy parallelism with one keyword!"

```java
// Sequential
list.stream()
    .filter(x -> x > 10)
    .map(x -> x * 2)
    .collect(Collectors.toList());

// Parallel - just add .parallel()
list.parallelStream()  // or .parallel()
    .filter(x -> x > 10)
    .map(x -> x * 2)
    .collect(Collectors.toList());
```

Looks simple, but there are **serious dangers**.

---

### How Parallel Streams Work

1. Splits data into chunks
2. Processes chunks in parallel (using ForkJoinPool.commonPool())
3. Combines results

```
     [1,2,3,4,5,6,7,8]
            ↓
    [1,2,3,4]  [5,6,7,8]
         ↓          ↓
    Thread 1    Thread 2
         ↓          ↓
    [results] [results]
            ↓
      [combined result]
```

---

### Danger #1: Shared Mutable State

```java
List<Integer> results = new ArrayList<>();  // NOT thread-safe!

list.parallelStream().forEach(x -> {
    results.add(x * 2);  // RACE CONDITION!
});
```

**Problem:** Multiple threads modifying `results` simultaneously → corruption!

**Solution:** Use thread-safe collection or proper reduction:
```java
List<Integer> results = list.parallelStream()
    .map(x -> x * 2)
    .collect(Collectors.toList());  // Thread-safe collector
```

---

### Danger #2: Blocking Operations

```java
list.parallelStream().forEach(item -> {
    makeNetworkCall(item);  // Blocks thread!
});
```

**Problem:** Parallel streams use common ForkJoinPool (limited threads). Blocking wastes threads.

**Solution:** Use `CompletableFuture` or custom thread pool for I/O.

---

### Danger #3: Wrong Operations

```java
// Sequential works fine
list.stream().forEach(item -> {
    // Operations happen in order
});

// Parallel may reorder!
list.parallelStream().forEach(item -> {
    // No guaranteed order!
});
```

**Use `forEachOrdered()` if order matters:**
```java
list.parallelStream().forEachOrdered(System.out::println);
```

---

### Danger #4: Too Small Data

```java
List<Integer> small = Arrays.asList(1, 2, 3, 4, 5);

// Sequential: fast
small.stream().map(x -> x * 2).collect(Collectors.toList());

// Parallel: SLOWER due to overhead
small.parallelStream().map(x -> x * 2).collect(Collectors.toList());
```

**Splitting, coordinating, and merging takes time!**

---

### Danger #5: Wrong Data Structure

Not all collections split efficiently:

**Good for parallel:**
- `ArrayList` - splits easily
- Arrays - splits easily
- `IntStream.range()` - splits easily

**Bad for parallel:**
- `LinkedList` - must traverse to split
- `Stream.iterate()` - sequential by nature
- `HashSet` - unpredictable splitting

---

### When Parallel Streams Are Good

✅ Use when ALL these are true:
1. **Large dataset** (thousands+ elements)
2. **CPU-intensive operations** (not I/O)
3. **No shared mutable state**
4. **Splittable data structure** (ArrayList, arrays)
5. **Order doesn't matter** (or using `forEachOrdered`)

**Example:**
```java
// Processing millions of numbers
List<Integer> huge = IntStream.range(0, 10_000_000)
    .boxed()
    .collect(Collectors.toList());

// Good use of parallel stream
double average = huge.parallelStream()
    .mapToInt(x -> expensiveComputation(x))
    .average()
    .orElse(0.0);
```

---

### When to Avoid Parallel Streams

❌ Avoid when:
- Small datasets (< 1000 elements)
- I/O operations
- Shared mutable state
- Order-dependent operations
- Already in a parallel context

**Default to sequential streams. Only parallelize after profiling shows benefit.**

---

### The Hidden Truth

Parallel streams use **ForkJoinPool.commonPool()** - shared across your entire application!

```java
// This blocks threads in common pool
list.parallelStream().forEach(x -> {
    Thread.sleep(1000);  // BAD!
});

// Now other parallel streams are starved
anotherList.parallelStream().map(...);  // Slow!
```

**Solution for I/O:** Use custom ForkJoinPool or ExecutorService.

---

## Immutable Objects in Concurrency

### The Core Problem

```java
class User {
    private String name;
    
    public void setName(String name) {
        this.name = name;  // Thread A writes
    }
    
    public String getName() {
        return name;  // Thread B reads
    }
}
```

**Problems:**
- Race conditions
- Need synchronization
- Visibility issues
- Complex to reason about

---

### The Solution: Immutability

**Once created, never changes.**

```java
final class User {
    private final String name;
    private final int age;
    
    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public String getName() { return name; }
    public int getAge() { return age; }
    
    // No setters!
}
```

---

### Why Immutable Objects Are Thread-Safe

1. **No state changes** → no race conditions
2. **No synchronization needed** → better performance
3. **Safe to share** → pass freely between threads
4. **Easier reasoning** → once valid, always valid

```java
User user = new User("Alice", 30);

// Thread 1
executor.submit(() -> System.out.println(user.getName()));

// Thread 2
executor.submit(() -> System.out.println(user.getAge()));

// No synchronization needed - user never changes!
```

---

### Creating Immutable Objects

**Rules:**
1. Make class `final` (can't be subclassed)
2. Make all fields `private final`
3. No setters
4. Deep copy mutable fields in constructor
5. Don't expose mutable fields

**Example with mutable field:**
```java
final class Order {
    private final List<String> items;
    
    public Order(List<String> items) {
        // Defensive copy
        this.items = new ArrayList<>(items);
    }
    
    public List<String> getItems() {
        // Return unmodifiable view
        return Collections.unmodifiableList(items);
    }
}
```

---

### Modifying Immutable Objects

Create a new instance instead:

```java
User user = new User("Alice", 30);

// Want to change age? Create new instance
User olderUser = new User(user.getName(), 31);
```

**Or use builder pattern:**
```java
User updated = user.toBuilder()
    .withAge(31)
    .build();
```

---

### Popular Immutable Classes in Java

- `String`
- `Integer`, `Long`, etc. (wrapper classes)
- `LocalDate`, `LocalDateTime`
- `BigInteger`, `BigDecimal`

**All thread-safe by default!**

---

### Performance Consideration

**Cost:** Creating new objects instead of modifying

**Benefit:** No synchronization overhead

**When immutability wins:**
- Objects shared across many threads
- Reading much more than writing
- Simple objects (not huge data structures)

---

### Real-World Example

```java
// Mutable (needs synchronization)
class Counter {
    private int count = 0;
    
    public synchronized void increment() {
        count++;
    }
}

// Immutable (no synchronization)
final class Counter {
    private final int count;
    
    public Counter(int count) {
        this.count = count;
    }
    
    public Counter increment() {
        return new Counter(count + 1);  // New instance
    }
}

// Usage with AtomicReference
AtomicReference<Counter> counter = new AtomicReference<>(new Counter(0));

// Thread-safe increment
counter.updateAndGet(c -> c.increment());
```

---

## Thread Confinement

### The Idea

**If only one thread accesses data, no synchronization needed!**

Three types:
1. **Stack confinement**
2. **ThreadLocal confinement**
3. **Object ownership**

---

### 1. Stack Confinement

Local variables are confined to the thread's stack.

```java
public void process() {
    int localVar = 0;  // Stack-confined
    
    for (int i = 0; i < 100; i++) {
        localVar += i;  // Only this thread accesses
    }
    
    System.out.println(localVar);  // Thread-safe!
}
```

**Why safe?** Each thread has its own stack. Local variables can't be accessed by other threads.

**Rule:** Don't let local variables escape:
```java
// BAD: local variable escapes
List<String> local = new ArrayList<>();
executor.submit(() -> {
    local.add("item");  // Different thread can access!
});
```

---

### 2. ThreadLocal

Each thread gets its own copy of a variable.

```java
ThreadLocal<SimpleDateFormat> formatter = ThreadLocal.withInitial(
    () -> new SimpleDateFormat("yyyy-MM-dd")
);

// Each thread gets its own SimpleDateFormat
public String formatDate(Date date) {
    return formatter.get().format(date);  // Thread-safe!
}
```

**How it works:**
```
Thread 1 → ThreadLocal → SimpleDateFormat instance #1
Thread 2 → ThreadLocal → SimpleDateFormat instance #2
Thread 3 → ThreadLocal → SimpleDateFormat instance #3
```

Each thread sees its own instance!

---

### ThreadLocal Use Cases

**1. Per-thread context:**
```java
ThreadLocal<User> currentUser = new ThreadLocal<>();

public void setCurrentUser(User user) {
    currentUser.set(user);
}

public User getCurrentUser() {
    return currentUser.get();
}
```

**2. Expensive object reuse:**
```java
// SimpleDateFormat is not thread-safe
ThreadLocal<SimpleDateFormat> formatter = ThreadLocal.withInitial(
    () -> new SimpleDateFormat("yyyy-MM-dd")
);
```

**3. Database connections:**
```java
ThreadLocal<Connection> connectionHolder = new ThreadLocal<>();
```

---

### ThreadLocal Pitfalls

**Memory leaks with thread pools:**
```java
ThreadLocal<HugeObject> data = new ThreadLocal<>();

executor.submit(() -> {
    data.set(new HugeObject());  // Set data
    // Thread returns to pool but HugeObject remains!
});
```

**Solution:** Always clean up!
```java
try {
    data.set(new HugeObject());
    doWork();
} finally {
    data.remove();  // IMPORTANT!
}
```

---

### 3. Object Ownership

Design your system so each object is "owned" by one thread.

```java
public class Pipeline {
    private final BlockingQueue<Task> queue = new LinkedBlockingQueue<>();
    
    public void start() {
        // Single consumer thread owns processing
        new Thread(() -> {
            while (true) {
                Task task = queue.take();
                process(task);  // Only this thread processes tasks
            }
        }).start();
    }
    
    public void addTask(Task task) {
        queue.offer(task);  // Transfers ownership via queue
    }
}
```

**Pattern:** Producer adds to queue (ownership transfer) → Consumer processes (exclusive ownership)

---

### Benefits of Thread Confinement

1. **No synchronization overhead**
2. **Simpler code** (no locks)
3. **Better performance**
4. **Easier to reason about**

**Drawback:** Only works when design supports it.

---

## Double-Checked Locking

### The Classic Problem: Lazy Initialization

```java
class Singleton {
    private static Singleton instance;
    
    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();  // Race condition!
        }
        return instance;
    }
}
```

**Problem:** Multiple threads can create multiple instances!

---

### Naive Solution: Synchronize Everything

```java
public static synchronized Singleton getInstance() {
    if (instance == null) {
        instance = new Singleton();
    }
    return instance;
}
```

**Problem:** Synchronization on every call (slow!), even after initialization.

---

### The Double-Checked Locking Pattern

```java
class Singleton {
    private static volatile Singleton instance;
    
    public static Singleton getInstance() {
        if (instance == null) {              // First check (no lock)
            synchronized (Singleton.class) {
                if (instance == null) {      // Second check (with lock)
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

**How it works:**
1. **First check:** Fast path, no lock (after initialization)
2. **Synchronized block:** Only entered if instance is null
3. **Second check:** Needed because another thread might have initialized while waiting for lock

---

### Why `volatile` is Critical

Without `volatile`, this can fail due to reordering:

```java
instance = new Singleton();
```

This actually does:
1. Allocate memory
2. Initialize object
3. Assign reference to `instance`

**Can be reordered to:**
1. Allocate memory
2. Assign reference to `instance` ← Partially constructed!
3. Initialize object

Another thread might see partially constructed object!

**`volatile` prevents this reordering.**

---

### The Better Solution: Initialization-on-Demand Holder

```java
class Singleton {
    private Singleton() {}
    
    private static class Holder {
        static final Singleton INSTANCE = new Singleton();
    }
    
    public static Singleton getInstance() {
        return Holder.INSTANCE;
    }
}
```

**How it works:**
- `Holder` class loaded only when `getInstance()` called
- JVM guarantees thread-safe class initialization
- No synchronization needed
- No `volatile` needed

**This is the preferred approach!**

---

### When to Use Each

**Synchronize everything:**
- Simple, but slow
- Use for rarely-accessed objects

**Double-checked locking:**
- Fast, but complex
- Use only if performance critical
- Always use `volatile`

**Initialization-on-Demand Holder:**
- Fast and simple
- Preferred for singletons
- JVM handles thread safety

**Enum singleton (best):**
```java
enum Singleton {
    INSTANCE;
    
    public void doSomething() {
        // ...
    }
}
```
- Simplest
- Thread-safe
- Prevents reflection attacks
- Serialization-safe

---

## Summary

### Key Takeaways

**1. Future & Callable**
- Use when you need return values from tasks
- `Future` represents eventual result
- Limited compared to `CompletableFuture`

**2. CompletableFuture**
- Modern async programming
- Chain operations without blocking
- Combine multiple async operations
- Built-in error handling

**3. Fork/Join Framework**
- Divide-and-conquer parallelism
- Best for recursive algorithms
- Automatic load balancing via work stealing
- Need good threshold tuning

**4. Work Stealing**
- Idle threads steal work from busy threads
- Better load balancing than fixed queues
- Used by ForkJoinPool and parallel streams
- Low contention design

**5. Parallel Streams**
- Easy parallelism but dangerous if misused
- Only for large, CPU-bound, independent tasks
- Avoid shared mutable state and I/O
- Profile before parallelizing!

**6. Immutable Objects**
- Thread-safe by design
- No synchronization needed
- Better for read-heavy scenarios
- Create new instances instead of modifying

**7. Thread Confinement**
- Best synchronization is no synchronization
- Stack confinement (local variables)
- ThreadLocal for per-thread data
- Object ownership patterns

**8. Double-Checked Locking**
- Optimization for lazy initialization
- Requires `volatile` to work correctly
- Prefer Initialization-on-Demand Holder
- Enum singleton is best for singletons

---

### Decision Guide

**Need async with return value?**
- Simple: `Future`
- Complex pipelines: `CompletableFuture`

**Need to parallelize recursion?**
- Use Fork/Join Framework

**Need to parallelize collection processing?**
- Small or I/O: Don't parallelize
- Large and CPU-bound: Maybe parallel streams (after profiling!)

**Need thread-safe object?**
- Shared across threads: Make it immutable
- Per-thread: Use ThreadLocal or stack confinement
- Complex state: Use proper locking

**Need singleton?**
- Best: Enum
- Good: Initialization-on-Demand Holder
- Avoid: Double-checked locking (unless you know why you need it)

---

### The Expert's Mindset

1. **Default to simplicity:** Use simple solutions first
2. **Profile before optimizing:** Don't assume parallelism helps
3. **Immutability first:** Avoid shared mutable state
4. **Understand the tools:** Know when each pattern fits
5. **Test thoroughly:** Concurrency bugs are subtle

**Remember:** The best concurrency bug is the one you prevent through good design!

---

## Further Reading

- Java Concurrency in Practice (Brian Goetz) - The Bible
- Effective Java (Joshua Bloch) - Item 83 (Lazy initialization)
- Java 8 in Action - CompletableFuture chapter
- Fork/Join Framework tutorial (Oracle)
