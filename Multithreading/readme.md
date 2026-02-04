# Java Multithreading - Complete Revision Notes

A comprehensive guide covering everything from basics to advanced concepts in Java concurrency.

---

## Table of Contents

### Part 1: Fundamentals
1. [Introduction to Multitasking](#1-introduction-to-multitasking)
2. [Thread Basics](#2-thread-basics)
3. [Thread Creation Methods](#3-thread-creation-methods)
4. [Thread Lifecycle](#4-thread-lifecycle)
5. [Thread Properties](#5-thread-properties)

### Part 2: Thread Control
6. [Thread Control Methods](#6-thread-control-methods)
7. [Thread Coordination](#7-thread-coordination)

### Part 3: Synchronization
8. [Synchronization Basics](#8-synchronization-basics)
9. [Inter-Thread Communication](#9-inter-thread-communication)
10. [Common Problems](#10-common-problems)

### Part 4: Java Memory Model
11. [Atomicity](#11-atomicity)
12. [Visibility Problem](#12-visibility-problem)
13. [Happens-Before Relationship](#13-happens-before-relationship)
14. [Reordering](#14-reordering)
15. [The volatile Keyword](#15-the-volatile-keyword)

### Part 5: Concurrent Utilities
16. [Thread Management (Executors)](#16-thread-management-executors)
17. [Locks & Synchronizers](#17-locks--synchronizers)
18. [Concurrent Collections](#18-concurrent-collections)
19. [Atomic Classes](#19-atomic-classes)

### Part 6: Advanced Patterns
20. [Future & Callable](#20-future--callable)
21. [CompletableFuture](#21-completablefuture)
22. [Fork/Join Framework](#22-forkjoin-framework)
23. [Parallel Streams](#23-parallel-streams)
24. [Design Patterns](#24-design-patterns)

### Part 7: Special Topics
25. [Daemon Threads](#25-daemon-threads)
26. [ThreadGroup & ThreadLocal](#26-threadgroup--threadlocal)
27. [Best Practices](#27-best-practices)

---

## Part 1: Fundamentals

---

## 1. Introduction to Multitasking

### What is Multitasking?
Executing several tasks simultaneously.

### Types

#### 1.1 Process-Based Multitasking
- Each task is a **separate independent process**
- Best at **OS level**
- Example: Typing code, listening to music, downloading file

#### 1.2 Thread-Based Multitasking
- Each task is an **independent part of same program**
- Each part is called a **Thread**
- Best at **programmatic level**
- Easier in Java (90% handled by API, 10% by programmer)

### Objective
**Improve system performance by reducing response time**

### Application Areas
1. Multimedia graphics
2. Video games
3. Web/Application servers

---

## 2. Thread Basics

### What is a Thread?
A **lightweight subprocess** - smallest unit of processing.

### Key Characteristics
- **Independent execution path** within a program
- Shares **process resources** (memory, files)
- Has its own **stack** but shares **heap**
- Can run **concurrently** with other threads

### Main Thread
- Every Java program has at least one thread: **main thread**
- Created by JVM to execute `main()` method
- Default priority: **5**
- Not a daemon thread

---

## 3. Thread Creation Methods

### Method 1: Extending Thread Class

```java
class MyThread extends Thread {
    public void run() {
        // Thread logic here
        System.out.println("Child thread");
    }
}

// Usage
MyThread t = new MyThread();
t.start();  // Creates new thread
```

### Method 2: Implementing Runnable Interface ⭐ (Preferred)

```java
class MyRunnable implements Runnable {
    public void run() {
        // Thread logic here
        System.out.println("Child thread");
    }
}

// Usage
MyRunnable r = new MyRunnable();
Thread t = new Thread(r);
t.start();
```

### Why Runnable is Preferred?
✅ **Allows extending other classes** (Java single inheritance)
✅ **Separation of concerns** (task vs thread management)
✅ **Better design** for object-oriented programming

---

## 4. Thread Lifecycle

### States

```
NEW → RUNNABLE → RUNNING → DEAD
         ↓           ↑
         ←  BLOCKED  ←
```

#### 1. NEW (Born State)
- Thread object created
- Not yet started
- Example: `Thread t = new Thread();`

#### 2. RUNNABLE
- After calling `start()`
- Waiting for CPU allocation
- Ready to run

#### 3. RUNNING
- Thread is executing
- CPU allocated by Thread Scheduler

#### 4. BLOCKED/WAITING
- Waiting for:
  - Lock acquisition
  - I/O completion
  - Another thread (join)
  - Notification (wait)
  - Sleep time to expire

#### 5. DEAD (Terminated)
- `run()` method completed
- Thread cannot be restarted

### Important Rules

#### start() vs run()

| `t.start()` | `t.run()` |
|-------------|-----------|
| Creates new thread | No new thread |
| Calls `run()` internally | Normal method call |
| Asynchronous | Synchronous |
| Can call only once | Can call multiple times |

#### Thread Scheduler
- **Part of JVM**
- Decides which thread executes first
- **Non-deterministic behavior** (vendor-dependent)
- Based on thread priorities

#### Cannot Restart Thread
```java
MyThread t = new MyThread();
t.start();  // ✅ Valid
// ... execution ...
t.start();  // ❌ IllegalThreadStateException
```

---

## 5. Thread Properties

### 5.1 Thread Name

#### Getting/Setting Name
```java
// Get name
String name = Thread.currentThread().getName();
String name = t.getName();

// Set name
Thread.currentThread().setName("MyMainThread");
t.setName("MyChildThread");
```

#### Default Names
- Main thread: `"main"`
- Other threads: `"Thread-0"`, `"Thread-1"`, etc.

#### Current Thread Reference
```java
Thread current = Thread.currentThread();
```

---

### 5.2 Thread Priority

#### Range
- **Valid: 1 to 10** (not 0 to 10)
- Higher priority = higher chance of execution

#### Constants
```java
Thread.MIN_PRIORITY = 1
Thread.NORM_PRIORITY = 5
Thread.MAX_PRIORITY = 10
```

❌ No `Thread.LOW_PRIORITY` or `Thread.HIGH_PRIORITY`

#### Methods
```java
int priority = t.getPriority();
t.setPriority(8);  // 1-10 only
t.setPriority(15); // ❌ IllegalArgumentException
```

#### Default Priority
- Main thread: **5**
- Child threads: **Inherit parent's priority**

```java
// Main thread priority: 5
Thread.currentThread().setPriority(9);
MyThread t = new MyThread();
// t's priority: 9 (inherited)
```

#### Important Notes
⚠️ Priority is a **suggestion**, not guarantee
⚠️ Thread Scheduler decides final execution order
⚠️ Same priority threads: non-deterministic order
⚠️ OS support varies (some ignore priorities)

---

### 5.3 Thread Constructors

```java
// 1. No-arg constructor
Thread t = new Thread();

// 2. With Runnable
Thread t = new Thread(Runnable r);

// 3. With name
Thread t = new Thread(String name);

// 4. Runnable + name
Thread t = new Thread(Runnable r, String name);

// 5. ThreadGroup + name
Thread t = new Thread(ThreadGroup g, String name);

// 6. ThreadGroup + Runnable
Thread t = new Thread(ThreadGroup g, Runnable r);

// 7. ThreadGroup + Runnable + name
Thread t = new Thread(ThreadGroup g, Runnable r, String name);

// 8. Full constructor
Thread t = new Thread(ThreadGroup g, Runnable r, String name, long stackSize);
```

---

## Part 2: Thread Control

---

## 6. Thread Control Methods

### 6.1 yield()

#### Purpose
Pause current thread to give chance to **same priority** waiting threads.

#### Signature
```java
public static native void yield();
```

#### Characteristics
- **Static method** (called on current thread)
- **Native method** (platform-dependent)
- **Not guaranteed** (hint to scheduler)
- If no waiting thread of same priority, same thread continues

#### Example
```java
class MyThread extends Thread {
    public void run() {
        for (int i = 0; i < 5; i++) {
            Thread.yield();  // Give chance to others
            System.out.println("Child");
        }
    }
}
```

#### When to Use
- CPU-intensive tasks
- To be "fair" to other threads
- Rarely used in practice

---

### 6.2 join()

#### Purpose
**Wait for another thread to complete** before proceeding.

#### Signatures
```java
public final void join() throws InterruptedException
public final void join(long ms) throws InterruptedException
public final void join(long ms, int ns) throws InterruptedException
```

#### Behavior
```java
MyThread t = new MyThread();
t.start();
t.join();  // Main thread waits until t completes
```

#### Example: Main Waits for Child
```java
class MyThread extends Thread {
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.println("Child: " + i);
            Thread.sleep(1000);
        }
    }
}

public static void main(String[] args) throws InterruptedException {
    MyThread t = new MyThread();
    t.start();
    t.join();  // Wait for child
    System.out.println("Main: Child completed");
}
```

#### Example: Child Waits for Main
```java
class MyThread extends Thread {
    static Thread mainThread;
    
    public void run() {
        try {
            mainThread.join();  // Child waits for main
        } catch (InterruptedException e) {}
        System.out.println("Child executing after main");
    }
}

public static void main(String[] args) throws InterruptedException {
    MyThread.mainThread = Thread.currentThread();
    MyThread t = new MyThread();
    t.start();
    
    for (int i = 0; i < 5; i++) {
        System.out.println("Main: " + i);
        Thread.sleep(1000);
    }
}
```

#### Deadlock Warning
```java
// Main thread
t.join();  // Waiting for t

// In thread t
mainThread.join();  // Waiting for main

// Result: DEADLOCK! Both wait forever
```

---

### 6.3 sleep()

#### Purpose
**Pause current thread** for specified time.

#### Signatures
```java
public static native void sleep(long ms) throws InterruptedException
public static void sleep(long ms, int ns) throws InterruptedException
```

#### Characteristics
- **Static method**
- **Native method** (first variant)
- **Throws InterruptedException**
- Thread enters **TIMED_WAITING** state

#### Example
```java
System.out.println("Start");
Thread.sleep(3000);  // Pause for 3 seconds
System.out.println("After 3 seconds");
```

#### Common Pattern: Polling
```java
while (!condition) {
    Thread.sleep(100);  // Check every 100ms
}
```

---

### 6.4 interrupt()

#### Purpose
**Interrupt a sleeping or waiting thread**.

#### Signature
```java
public void interrupt();
```

#### What It Does
- Sets thread's **interrupted flag** to true
- If thread is sleeping/waiting: throws `InterruptedException`
- If thread is running normally: just sets flag

#### Example 1: Interrupt During Sleep
```java
class MyThread extends Thread {
    public void run() {
        try {
            for (int i = 0; i < 5; i++) {
                System.out.println("Working: " + i);
                Thread.sleep(2000);
            }
        } catch (InterruptedException e) {
            System.out.println("I got interrupted!");
        }
    }
}

MyThread t = new MyThread();
t.start();
t.interrupt();  // Interrupt immediately
```

#### Example 2: Interrupt Before Sleep
```java
class MyThread extends Thread {
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.println("Working: " + i);
        }
        System.out.println("Going to sleep");
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            System.out.println("Interrupted during sleep!");
        }
    }
}

MyThread t = new MyThread();
t.start();
t.interrupt();  // Interrupt flag set, exception when sleep() called
```

#### Checking Interrupt Status
```java
if (Thread.interrupted()) {  // Checks and clears flag
    // Handle interruption
}

if (Thread.currentThread().isInterrupted()) {  // Just checks
    // Handle interruption
}
```

---

## 7. Thread Coordination

### Comparison Table

| Property | yield() | join() | sleep() |
|----------|---------|--------|---------|
| **Purpose** | Give chance to same priority | Wait for thread completion | Pause for time |
| **Static?** | ✅ Yes | ❌ No | ✅ Yes |
| **Final?** | ❌ No | ✅ Yes | ❌ No |
| **Overloaded?** | ❌ No | ✅ Yes | ✅ Yes |
| **Throws InterruptedException?** | ❌ No | ✅ Yes | ✅ Yes |
| **Native?** | ✅ Yes | ❌ No | ✅ Yes (one variant) |
| **Releases Lock?** | ❌ No | ❌ No | ❌ No |

### Key Insight: Lock Release
❌ `yield()` - Does NOT release lock
❌ `join()` - Does NOT release lock
❌ `sleep()` - Does NOT release lock
✅ `wait()` - DOES release lock (covered later)

---

## Part 3: Synchronization

---

## 8. Synchronization Basics

### The Problem: Data Inconsistency

```java
class Counter {
    private int count = 0;
    
    public void increment() {
        count++;  // NOT atomic! Race condition!
    }
}

// Thread 1: reads 0, adds 1, writes 1
// Thread 2: reads 0, adds 1, writes 1
// Result: count = 1 (should be 2!)
```

---

### 8.1 The synchronized Keyword

#### What It Does
- **Only one thread** can execute synchronized method/block at a time
- Uses **object lock** mechanism
- Prevents **data inconsistency**

#### Where to Use
✅ Methods: `public synchronized void method()`
✅ Blocks: `synchronized(object) { ... }`
❌ Classes: Cannot use on class declaration
❌ Variables: Cannot use on variable declaration

---

### 8.2 Object Lock Concept

#### Every Object Has a Lock
```java
Object obj = new Object();
// obj has a unique lock
```

#### How It Works
1. Thread **acquires lock** before executing synchronized method
2. Thread executes synchronized code
3. Thread **releases lock** after completion
4. Other threads wait for lock

---

### 8.3 Synchronized Method Example

```java
class Display {
    public synchronized void wish(String name) {
        for (int i = 0; i < 5; i++) {
            System.out.print("Good Morning: ");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {}
            System.out.println(name);
        }
    }
}

// Thread 1
Display d = new Display();
new Thread(() -> d.wish("Dhoni")).start();

// Thread 2
new Thread(() -> d.wish("Yuvaraj")).start();
```

#### Without synchronized
```
Good Morning: Good Morning: Dhoni
Yuvaraj
Good Morning: Good Morning: Dhoni
Yuvaraj
// Irregular output
```

#### With synchronized
```
Good Morning: Dhoni
Good Morning: Dhoni
Good Morning: Dhoni
Good Morning: Dhoni
Good Morning: Dhoni
Good Morning: Yuvaraj
Good Morning: Yuvaraj
Good Morning: Yuvaraj
Good Morning: Yuvaraj
Good Morning: Yuvaraj
// Regular output
```

---

### 8.4 Important Rules

#### Rule 1: Same Object Required
```java
Display d1 = new Display();
Display d2 = new Display();

Thread t1 = new Thread(() -> d1.wish("A"));  // Lock on d1
Thread t2 = new Thread(() -> d2.wish("B"));  // Lock on d2

// Both run simultaneously! Different objects = different locks
```

**Conclusion:** Synchronization works only when threads operate on **same object**.

#### Rule 2: Synchronized vs Non-Synchronized
While one thread executes **synchronized method**:
- ❌ Other threads **cannot** execute **any synchronized method** on same object
- ✅ Other threads **can** execute **non-synchronized methods**

```java
class Demo {
    public synchronized void method1() { }  // Lock required
    public synchronized void method2() { }  // Lock required
    public void method3() { }               // No lock required
}

// Thread 1 executing method1()
// Thread 2 cannot execute method1() or method2()
// Thread 2 CAN execute method3()
```

---

### 8.5 Class Level Lock

#### Static Synchronized Methods

```java
class Display {
    public static synchronized void method() {
        // Class level lock required
    }
}
```

#### How It Works
- Every **class** has a unique lock (separate from object lock)
- Thread needs **class level lock** for static synchronized methods
- **Class lock ≠ Object lock** (independent)

#### Rules
While thread executes **static synchronized method**:
- ❌ Cannot execute **other static synchronized methods**
- ✅ Can execute **instance synchronized methods** (different lock!)
- ✅ Can execute **normal static methods**
- ✅ Can execute **normal instance methods**

---

### 8.6 Synchronized Block

#### Why Use Blocks?
If only **few lines** need synchronization, use block instead of synchronizing entire method.

**Advantage:** Reduces waiting time, improves performance.

#### Syntax Options

**Option 1: Current Object Lock**
```java
synchronized(this) {
    // Critical section
}
```

**Option 2: Specific Object Lock**
```java
synchronized(objectRef) {
    // Critical section
}
```

**Option 3: Class Level Lock**
```java
synchronized(ClassName.class) {
    // Critical section
}
```

#### Rules
- Argument must be **object reference** or **ClassName.class**
- ❌ **Cannot use primitives**

```java
int x = 10;
synchronized(x) {  // ❌ Compile Error!
    // Error: unexpected type
    // Found: int
    // Required: reference
}
```

---

### 8.7 Advantages vs Disadvantages

#### Advantages ✅
- Solves **data inconsistency** problems
- **Thread-safe** operations
- Prevents **race conditions**

#### Disadvantages ❌
- Increases **waiting time**
- Reduces **performance** (threads wait for lock)
- Can cause **deadlock** if not careful

**Recommendation:** Use synchronization only when necessary!

---

## 9. Inter-Thread Communication

### 9.1 The Three Methods

Methods for thread communication:
- `wait()` - Wait for notification
- `notify()` - Wake up one waiting thread
- `notifyAll()` - Wake up all waiting threads

---

### 9.2 Why in Object Class?

**Question:** Why are these methods in `Object` class, not `Thread` class?

**Answer:** Because threads call these methods on **shared objects**, not on threads themselves.

```java
// Threads communicate via shared object
SharedObject obj = new SharedObject();

// Thread 1
synchronized(obj) {
    obj.wait();  // Wait on the object
}

// Thread 2
synchronized(obj) {
    obj.notify();  // Notify on the object
}
```

---

### 9.3 Method Signatures

```java
// Wait methods
public final void wait() throws InterruptedException
public final void wait(long ms) throws InterruptedException
public final void wait(long ms, int ns) throws InterruptedException

// Notify methods
public final void notify()
public final void notifyAll()
```

---

### 9.4 Conditions to Call

**MUST be inside synchronized context!**

```java
// ✅ Valid
synchronized(obj) {
    obj.wait();
}

// ❌ Invalid - IllegalMonitorStateException
obj.wait();  // Not inside synchronized!
```

**Why?** Thread must **own the lock** of the object to call these methods.

---

### 9.5 Lock Release Behavior

| Method | Releases Lock? | When? |
|--------|---------------|-------|
| `wait()` | ✅ Yes | Immediately |
| `notify()` | ✅ Yes | After synchronized block ends |
| `notifyAll()` | ✅ Yes | After synchronized block ends |
| `yield()` | ❌ No | N/A |
| `join()` | ❌ No | N/A |
| `sleep()` | ❌ No | N/A |

**Important:** Only `wait()`, `notify()`, `notifyAll()` release lock!

---

### 9.6 Example: Producer-Consumer Pattern

```java
class ThreadB extends Thread {
    int total = 0;
    
    public void run() {
        synchronized(this) {
            System.out.println("Child: Starting calculation");
            for (int i = 1; i <= 100; i++) {
                total += i;
            }
            System.out.println("Child: Giving notification");
            this.notify();  // Wake up waiting thread
        }
    }
}

public class ThreadA {
    public static void main(String[] args) throws InterruptedException {
        ThreadB b = new ThreadB();
        b.start();
        
        synchronized(b) {
            System.out.println("Main: Calling wait()");
            b.wait();  // Wait for notification
            System.out.println("Main: Got notification");
            System.out.println("Total: " + b.total);
        }
    }
}
```

**Output:**
```
Main: Calling wait()
Child: Starting calculation
Child: Giving notification
Main: Got notification
Total: 5050
```

---

### 9.7 notify() vs notifyAll()

#### notify()
- Wakes up **ONE** waiting thread
- **Which one?** JVM decides (unpredictable)

#### notifyAll()
- Wakes up **ALL** waiting threads
- They compete for lock one by one

#### When to Use
- Use `notify()` when **only one thread** should proceed
- Use `notifyAll()` when **all threads** should be woken up

```java
// Multiple threads waiting
synchronized(obj) {
    obj.notifyAll();  // Wake everyone up
}
```

---

### 9.8 Common Mistakes

#### Mistake 1: Wrong Lock Object
```java
// ❌ Wrong
synchronized(obj1) {
    obj2.wait();  // IllegalMonitorStateException!
}

// ✅ Correct
synchronized(obj) {
    obj.wait();
}
```

#### Mistake 2: Not in Synchronized Block
```java
// ❌ Wrong
obj.wait();  // IllegalMonitorStateException!

// ✅ Correct
synchronized(obj) {
    obj.wait();
}
```

---

### 9.9 True/False Questions

1. Once a Thread calls `wait()` on any Object, it enters waiting state **without releasing lock**?
   - **FALSE** - It immediately releases lock

2. Once a Thread calls `wait()` on any Object, it releases lock but **may not immediately**?
   - **FALSE** - It releases immediately

3. Once a Thread calls `wait()` on any Object, it immediately releases **all locks** it has?
   - **FALSE** - Only that particular object's lock

4. Once a Thread calls `wait()` on any Object, it immediately releases **that particular object's lock**?
   - **TRUE** ✅

5. Once a Thread calls `notify()` on any Object, it **immediately** releases the lock?
   - **FALSE** - Releases after synchronized block ends

6. Once a Thread calls `notify()` on any Object, it releases lock but **may not immediately**?
   - **TRUE** ✅

---

## 10. Common Problems

### 10.1 Deadlock

#### Definition
Two or more threads **waiting for each other forever** - circular dependency.

#### Example
```java
class A {
    public synchronized void foo(B b) {
        System.out.println("Thread1 in foo()");
        Thread.sleep(2000);
        b.last();  // Needs lock on B
    }
    public synchronized void last() {
        System.out.println("A.last()");
    }
}

class B {
    public synchronized void bar(A a) {
        System.out.println("Thread2 in bar()");
        Thread.sleep(2000);
        a.last();  // Needs lock on A
    }
    public synchronized void last() {
        System.out.println("B.last()");
    }
}

// Main thread
A a = new A();
B b = new B();
a.foo(b);  // Holds lock on A, needs lock on B

// Child thread
b.bar(a);  // Holds lock on B, needs lock on A

// DEADLOCK! Both wait forever
```

#### Deadlock Conditions (All must be true)
1. **Mutual Exclusion** - Resource can't be shared
2. **Hold and Wait** - Holding resource while waiting for another
3. **No Preemption** - Can't force release of resource
4. **Circular Wait** - Circular chain of waiting threads

#### Prevention Strategies
✅ **Avoid nested locks** - Don't acquire multiple locks
✅ **Lock ordering** - Always acquire locks in same order
✅ **Timeouts** - Use `tryLock()` with timeout
✅ **Deadlock detection** - Monitor and break deadlock

#### Key Point
`synchronized` keyword is **primary cause** of deadlock. Use carefully!

---

### 10.2 Deadlock vs Starvation

| Feature | Deadlock | Starvation |
|---------|----------|------------|
| **Definition** | Threads wait forever | Low-priority thread waits too long |
| **Cause** | Circular lock dependency | Thread priority/scheduling |
| **Can End?** | ❌ Never | ✅ Eventually (might) |
| **Threads Involved** | Multiple (≥2) | Usually one |
| **Solution** | Prevent/detect | Fair scheduling, priority adjustment |

---

### 10.3 Race Condition

#### Definition
Multiple threads access **shared data** simultaneously, causing **inconsistent results**.

#### Example
```java
class Counter {
    private int count = 0;
    
    public void increment() {
        count++;  // Race condition!
        // Three operations:
        // 1. Read count
        // 2. Add 1
        // 3. Write count
    }
}
```

#### Solution
Use `synchronized` keyword:
```java
public synchronized void increment() {
    count++;  // Thread-safe
}
```

---

## Part 4: Java Memory Model

---

## 11. Atomicity

### Definition
An operation happens as **single, indivisible unit** - all or nothing.

### Atomic Operations
✅ Reading/writing **reference variables**
✅ Reading/writing **primitive variables** (except long/double on 32-bit)
✅ Reading/writing **volatile variables** (including long/double)

### Non-Atomic Operations
❌ `count++` (read-modify-write)
❌ `count = count + 1`
❌ `if (count == 0) count = 1;` (check-then-act)
❌ `long x = 123456789L` (on 32-bit JVM - two operations)

### Example: Why count++ Fails
```java
private int count = 0;

public void increment() {
    count++;  // NOT atomic!
}

// Thread 1: reads 5 → calculates 6 → writes 6
// Thread 2: reads 5 → calculates 6 → writes 6
// Result: 6 (should be 7!)
```

### Solutions
```java
// Solution 1: synchronized
public synchronized void increment() {
    count++;
}

// Solution 2: AtomicInteger
private AtomicInteger count = new AtomicInteger(0);
public void increment() {
    count.incrementAndGet();
}
```

### Key Point
**volatile ≠ Atomic**
```java
private volatile int count = 0;

public void increment() {
    count++;  // Still NOT atomic!
}
```

---

## 12. Visibility Problem

### The Core Issue
Each CPU core has its own **cache**. Changes in one cache may not be visible to other caches.

```
Main Memory:  [flag = false]
                ↓
       ┌────────┴────────┐
   Core 1 Cache      Core 2 Cache
   [flag = false]    [flag = false]
      ↓                 ↓
   Thread 1          Thread 2
```

### Example Problem
```java
private static boolean flag = false;
private static int number = 0;

// Thread 1 (Writer)
number = 42;
flag = true;

// Thread 2 (Reader)
while (!flag) {
    // Might loop forever!
}
System.out.println(number);  // Might print 0!
```

**Why?**
- Thread 2 may never see `flag = true`
- Even if it sees flag, might not see updated `number`

### Solution: volatile
```java
private static volatile boolean flag = false;
```

Now visibility is guaranteed!

---

## 13. Happens-Before Relationship

### Definition
If Action A **happens-before** Action B, then:
- A's effects are **visible** to B
- A is **ordered before** B

### Key Rules

#### 1. Program Order Rule
Actions in a thread happen-before subsequent actions in that thread.

```java
int a = 1;        // Action 1
int b = 2;        // Action 2
int c = a + b;    // Action 3
// 1 happens-before 2 happens-before 3
```

#### 2. Volatile Variable Rule
Write to volatile happens-before subsequent read of that volatile.

```java
volatile boolean ready = false;
int data = 0;

// Thread 1
data = 42;      // Action A
ready = true;   // Action B (volatile write)

// Thread 2
if (ready) {    // Action C (volatile read)
    print(data); // Action D - sees 42!
}

// Chain: A → B → C → D
```

#### 3. Thread Start Rule
`thread.start()` happens-before any action in started thread.

#### 4. Thread Join Rule
All actions in thread happen-before `join()` returns.

#### 5. Monitor Lock Rule
Unlock happens-before subsequent lock of same monitor.

### Example Chain
```java
volatile boolean ready = false;
int x = 0;

// Thread 1
x = 100;        // A
ready = true;   // B (volatile)

// Thread 2
if (ready) {    // C (volatile)
    print(x);   // D
}

// Happens-before chain:
// A → B (program order)
// B → C (volatile rule)
// C → D (program order)
// Therefore: A → D (guaranteed x = 100)
```

---

## 14. Reordering

### What is Reordering?
Compiler/CPU can **reorder instructions** for optimization, as long as single-threaded behavior doesn't change.

### Example
```java
// What you write:
int a = 1;
int b = 2;
int c = a + b;

// What might execute:
int a = 1;
int c = a + b;  // Moved up!
int b = 2;      // Moved down!
```

In single thread: No visible difference
In multiple threads: **Chaos!**

### The Problem
```java
private int x = 0, y = 0;
private int a = 0, b = 0;

// Thread 1
a = 1;
x = b;

// Thread 2
b = 1;
y = a;
```

**Possible outputs:**
- x=1, y=1 ✓
- x=0, y=1 ✓
- x=1, y=0 ✓
- **x=0, y=0** ✓ (Due to reordering!)

### How volatile Helps
```java
private volatile int x = 0, y = 0;
// Now reordering is restricted
```

`volatile` creates a **memory barrier** that prevents certain reorderings.

---

## 15. The volatile Keyword

### What volatile Guarantees

#### 1. Visibility
- Writes are **immediately visible** to all threads
- Reads always get **latest value** from main memory

#### 2. Ordering (Happens-Before)
- Prevents certain **reorderings**
- Establishes **happens-before** relationships

### How It Works

**On Write:**
```java
volatile boolean flag = false;
flag = true;  // Flushes to main memory immediately
```
- Value written to **main memory** (not just cache)
- All previous writes also flushed (happens-before)

**On Read:**
```java
if (flag) {  // Reads from main memory
    // ...
}
```
- Value read from **main memory** (not cache)
- Invalidates cached values

### Simple Example
```java
public class VolatileExample {
    private volatile boolean running = true;
    
    public void run() {
        while (running) {
            // Do work
        }
        System.out.println("Stopped!");
    }
    
    public void stop() {
        running = false;  // Immediately visible
    }
}
```

**Without volatile:** Loop might never see `running = false`
**With volatile:** Change is guaranteed visible

---

### When to Use volatile

✅ **Status flags**
```java
private volatile boolean shutdown = false;
```

✅ **One writer, multiple readers**
```java
private volatile int temperature;

// One thread writes
public void updateTemp(int temp) {
    temperature = temp;
}

// Many threads read
public int getTemp() {
    return temperature;
}
```

✅ **Safe publication**
```java
private volatile Settings settings;

public void updateSettings(Settings s) {
    settings = s;  // Safely published
}
```

### Requirements for volatile
Use volatile when **ALL** are true:
- Writes don't depend on current value
- Variable doesn't participate in invariants with other variables
- Reads don't require locking

---

### When NOT to Use volatile

❌ **Compound operations**
```java
private volatile int counter = 0;

public void increment() {
    counter++;  // NOT atomic! Race condition!
}
```

❌ **Check-then-act**
```java
private volatile int value = 0;

public void updateIfZero() {
    if (value == 0) {     // Check
        value = 42;       // Act
    }  // Race condition between check and act!
}
```

❌ **Multiple variables with invariants**
```java
private volatile int lower = 0;
private volatile int upper = 10;

public void setRange(int l, int u) {
    lower = l;  // Invariant lower <= upper
    upper = u;  // Can be violated between these lines!
}
```

### What to Use Instead
- **For counters:** `AtomicInteger`
- **For compound operations:** `synchronized` or `Lock`
- **For multiple variables:** `synchronized` block

---

## Part 5: Concurrent Utilities

---

## 16. Thread Management (Executors)

### Why Not Manual Threads?
```java
// BAD: Manual thread creation
for (int i = 0; i < 1000; i++) {
    new Thread(() -> process()).start();
    // Creates 1000 threads! System overwhelmed!
}
```

**Problems:**
- Thread creation is **expensive**
- Too many threads → **resource exhaustion**
- No **control** or **cancellation**
- **Resource leaks** if not managed

### Solution: Executor Framework
Manages a **pool of worker threads** for you.

---

### 16.1 Basic Executor

```java
Executor executor = ...;
executor.execute(() -> doWork());
```

---

### 16.2 ExecutorService

Enhanced with lifecycle management.

```java
ExecutorService executor = Executors.newFixedThreadPool(4);

// Submit task
Future<String> future = executor.submit(() -> "Result");

// Shutdown
executor.shutdown();  // No new tasks
executor.awaitTermination(60, TimeUnit.SECONDS);
```

---

### 16.3 Thread Pools

#### Fixed Thread Pool
Fixed number of threads.

```java
ExecutorService executor = Executors.newFixedThreadPool(4);
// Best for CPU-bound tasks
// Pool size = number of CPU cores
```

**Use:** CPU-intensive tasks (calculations, processing)

#### Cached Thread Pool
Creates threads as needed, reuses idle threads.

```java
ExecutorService executor = Executors.newCachedThreadPool();
// Best for short-lived, I/O tasks
```

**Use:** Many short I/O tasks
⚠️ Can create unlimited threads!

#### Single Thread Executor
One thread, executes tasks sequentially.

```java
ExecutorService executor = Executors.newSingleThreadExecutor();
// Guarantees order
```

**Use:** Tasks must execute in order (logging, events)

#### Scheduled Thread Pool
Schedule tasks with delay or periodically.

```java
ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(2);

// Run once after 5 seconds
scheduler.schedule(() -> task(), 5, TimeUnit.SECONDS);

// Run every 10 seconds
scheduler.scheduleAtFixedRate(() -> task(), 1, 10, TimeUnit.SECONDS);

// Run 10 seconds after previous completes
scheduler.scheduleWithFixedDelay(() -> task(), 1, 10, TimeUnit.SECONDS);
```

**Use:** Periodic tasks, scheduled jobs, timeouts

---

### 16.4 Best Practices

#### Always Shutdown
```java
ExecutorService executor = Executors.newFixedThreadPool(10);
try {
    // Use executor
} finally {
    executor.shutdown();
    if (!executor.awaitTermination(60, TimeUnit.SECONDS)) {
        executor.shutdownNow();
    }
}
```

#### Choose Right Pool
```java
// CPU-bound: Fixed pool with core count
int cores = Runtime.getRuntime().availableProcessors();
ExecutorService cpu = Executors.newFixedThreadPool(cores);

// I/O-bound: Cached pool or larger fixed pool
ExecutorService io = Executors.newCachedThreadPool();
```

---

## 17. Locks & Synchronizers

### 17.1 ReentrantLock

More flexible than `synchronized`.

```java
ReentrantLock lock = new ReentrantLock();

lock.lock();
try {
    // Critical section
} finally {
    lock.unlock();  // ALWAYS in finally!
}
```

#### Advanced Features
```java
// Try without blocking
if (lock.tryLock()) {
    try {
        // Got lock
    } finally {
        lock.unlock();
    }
} else {
    // Didn't get lock
}

// Try with timeout
if (lock.tryLock(2, TimeUnit.SECONDS)) {
    // ...
}

// Interruptible
try {
    lock.lockInterruptibly();
    // ...
} catch (InterruptedException e) {
    // Interrupted while waiting
}
```

---

### 17.2 ReadWriteLock

Multiple readers OR one writer.

```java
ReadWriteLock rwLock = new ReentrantReadWriteLock();
Lock readLock = rwLock.readLock();
Lock writeLock = rwLock.writeLock();

// Many readers can read simultaneously
readLock.lock();
try {
    return data;
} finally {
    readLock.unlock();
}

// Only one writer
writeLock.lock();
try {
    data = newValue;
} finally {
    writeLock.unlock();
}
```

**Use:** Read-heavy scenarios (90%+ reads)

---

### 17.3 Semaphore

Limits concurrent access to resource.

```java
Semaphore semaphore = new Semaphore(3);  // 3 permits

semaphore.acquire();  // Get permit
try {
    // Use resource
} finally {
    semaphore.release();  // Return permit
}
```

**Use:** Connection pools, rate limiting

---

### 17.4 CountDownLatch

Wait for N events to complete. **One-time use**.

```java
CountDownLatch latch = new CountDownLatch(3);

// Worker threads
new Thread(() -> {
    doWork();
    latch.countDown();  // Signal completion
}).start();

// Main thread
latch.await();  // Wait for all 3 to complete
```

**Use:** Waiting for multiple tasks before proceeding

---

### 17.5 CyclicBarrier

All threads wait at barrier, then proceed together. **Reusable**.

```java
CyclicBarrier barrier = new CyclicBarrier(4, () -> {
    System.out.println("All threads reached barrier!");
});

// Each thread
barrier.await();  // Wait for all threads
```

**Use:** Multi-phase computations, synchronized processing

---

### 17.6 Phaser

Advanced synchronizer with dynamic parties.

```java
Phaser phaser = new Phaser(1);  // 1 party initially

// Register parties dynamically
phaser.register();

// Arrive and wait
phaser.arriveAndAwaitAdvance();

// Done - deregister
phaser.arriveAndDeregister();
```

**Use:** Complex multi-phase workflows

---

## 18. Concurrent Collections

### 18.1 ConcurrentHashMap

Thread-safe HashMap with high concurrency.

```java
ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();

// Thread-safe operations
map.put("key", 1);
map.get("key");

// Atomic compound operations
map.putIfAbsent("key", 1);
map.computeIfAbsent("key", k -> compute(k));
map.merge("key", 1, Integer::sum);
```

**Performance:** Multiple threads can read/write different segments simultaneously!

---

### 18.2 CopyOnWriteArrayList

Thread-safe list for read-heavy scenarios.

```java
CopyOnWriteArrayList<String> list = new CopyOnWriteArrayList<>();

list.add("item");  // Expensive (copies array)

// Fast iteration, no locking
for (String item : list) {
    process(item);
}
```

**Use:** 90%+ reads, small lists (event listeners)
**Don't use:** Frequent writes, large lists

---

### 18.3 BlockingQueue

Thread-safe queue with blocking operations.

```java
BlockingQueue<Task> queue = new LinkedBlockingQueue<>();

// Producer
queue.put(task);  // Blocks if full

// Consumer
Task task = queue.take();  // Blocks if empty
```

**Implementations:**
- `ArrayBlockingQueue` - Fixed capacity
- `LinkedBlockingQueue` - Optionally bounded
- `PriorityBlockingQueue` - Priority-ordered
- `SynchronousQueue` - No capacity, direct handoff

**Use:** Producer-consumer patterns

---

## 19. Atomic Classes

Lock-free, thread-safe atomic operations.

### 19.1 AtomicInteger

```java
AtomicInteger counter = new AtomicInteger(0);

counter.incrementAndGet();  // Atomic ++counter
counter.getAndIncrement();  // Atomic counter++
counter.addAndGet(5);       // Atomic counter += 5
counter.compareAndSet(0, 1); // CAS operation
```

---

### 19.2 AtomicLong

Same as AtomicInteger for `long` values.

```java
AtomicLong requestCount = new AtomicLong(0);
requestCount.incrementAndGet();
```

---

### 19.3 AtomicBoolean

```java
AtomicBoolean initialized = new AtomicBoolean(false);

if (initialized.compareAndSet(false, true)) {
    // Only one thread executes this
    performInitialization();
}
```

---

### 19.4 AtomicReference

Thread-safe reference to object.

```java
AtomicReference<Settings> settings = new AtomicReference<>();

settings.set(newSettings);  // Atomic update
Settings current = settings.get();

// CAS pattern
Settings old = settings.get();
settings.compareAndSet(old, newSettings);
```

---

### Atomic vs Synchronized

| Feature | Atomic | Synchronized |
|---------|--------|--------------|
| Lock-free | ✅ Yes | ❌ No |
| Low contention performance | ✅ Better | ❌ Slower |
| High contention performance | ❌ Worse | ✅ Better |
| Use for | Simple operations | Complex operations |

---

## Part 6: Advanced Patterns

---

## 20. Future & Callable

### The Problem with Runnable
- Can't return value
- Can't throw checked exceptions

### Callable: Better Runnable
```java
Callable<Integer> task = () -> {
    return expensiveComputation();
};
```

### Future: Promise of Result
```java
ExecutorService executor = Executors.newFixedThreadPool(2);

Future<Integer> future = executor.submit(() -> {
    Thread.sleep(2000);
    return 42;
});

// Do other work
doSomethingElse();

// Get result (blocks if not ready)
Integer result = future.get();
```

### Future Operations
```java
future.isDone();        // Check if complete
future.isCancelled();   // Check if cancelled
future.cancel(true);    // Cancel task
future.get(5, TimeUnit.SECONDS);  // Get with timeout
```

### Limitations
- Can't chain operations
- Can't combine multiple futures
- Blocking `get()`
- No callbacks

**Solution:** CompletableFuture

---

## 21. CompletableFuture

### Async Pipeline
```java
CompletableFuture.supplyAsync(() -> fetchData())
    .thenApply(data -> transform(data))
    .thenAccept(result -> save(result))
    .exceptionally(ex -> handleError(ex));
```

### Creating
```java
// Already completed
CompletableFuture<String> future = CompletableFuture.completedFuture("Hello");

// Run async (no return)
CompletableFuture<Void> future = CompletableFuture.runAsync(() -> doWork());

// Supply async (with return)
CompletableFuture<Integer> future = CompletableFuture.supplyAsync(() -> compute());
```

### Chaining

**thenApply** - Transform result
```java
CompletableFuture<Integer> future = CompletableFuture
    .supplyAsync(() -> 5)
    .thenApply(n -> n * 2)
    .thenApply(n -> n + 3);  // Result: 13
```

**thenAccept** - Consume result
```java
future.thenAccept(result -> System.out.println(result));
```

**thenRun** - Just run after
```java
future.thenRun(() -> System.out.println("Done"));
```

### Combining

**thenCombine** - Combine two futures
```java
CompletableFuture<Integer> f1 = CompletableFuture.supplyAsync(() -> 10);
CompletableFuture<Integer> f2 = CompletableFuture.supplyAsync(() -> 20);

CompletableFuture<Integer> result = f1.thenCombine(f2, (a, b) -> a + b);
```

**thenCompose** - Chain dependent futures
```java
CompletableFuture<User> user = CompletableFuture
    .supplyAsync(() -> getUserId())
    .thenCompose(id -> fetchUserDetails(id));
```

**allOf** - Wait for all
```java
CompletableFuture<Void> all = CompletableFuture.allOf(f1, f2, f3);
all.join();  // Wait for all
```

**anyOf** - First to complete
```java
CompletableFuture<Object> fastest = CompletableFuture.anyOf(f1, f2, f3);
```

### Error Handling
```java
future
    .exceptionally(ex -> "Default")
    .handle((result, ex) -> {
        if (ex != null) return "Error";
        return result;
    });
```

---

## 22. Fork/Join Framework

### Divide-and-Conquer Pattern
```
if (task is small) {
    solve directly
} else {
    split into subtasks
    fork subtasks (parallel)
    join results
}
```

### RecursiveTask Example
```java
class SumTask extends RecursiveTask<Long> {
    private int[] array;
    private int start, end;
    private static final int THRESHOLD = 1000;
    
    protected Long compute() {
        if (end - start <= THRESHOLD) {
            // Small enough: compute directly
            long sum = 0;
            for (int i = start; i < end; i++) {
                sum += array[i];
            }
            return sum;
        } else {
            // Split
            int mid = (start + end) / 2;
            SumTask left = new SumTask(array, start, mid);
            SumTask right = new SumTask(array, mid, end);
            
            left.fork();  // Execute in parallel
            long rightResult = right.compute();
            long leftResult = left.join();
            
            return leftResult + rightResult;
        }
    }
}

// Usage
ForkJoinPool pool = new ForkJoinPool();
Long result = pool.invoke(new SumTask(array, 0, array.length));
```

### When to Use
✅ Large arrays/collections
✅ Recursive algorithms
✅ Tasks that split evenly

❌ I/O operations
❌ Uneven splits
❌ Too fine-grained

---

## 23. Parallel Streams

### Simple Parallelism
```java
// Sequential
list.stream()
    .filter(x -> x > 10)
    .map(x -> x * 2)
    .collect(Collectors.toList());

// Parallel
list.parallelStream()
    .filter(x -> x > 10)
    .map(x -> x * 2)
    .collect(Collectors.toList());
```

### Dangers

**Danger 1: Shared Mutable State**
```java
List<Integer> results = new ArrayList<>();  // NOT thread-safe!
list.parallelStream().forEach(x -> results.add(x));  // RACE CONDITION!
```

**Danger 2: Blocking Operations**
```java
list.parallelStream().forEach(item -> makeNetworkCall(item));  // BAD!
```

**Danger 3: Too Small Data**
```java
List<Integer> small = Arrays.asList(1, 2, 3);
small.parallelStream()...  // Slower due to overhead!
```

**Danger 4: Wrong Order**
```java
list.parallelStream().forEach(...);  // No order guarantee!
// Use forEachOrdered() if order matters
```

**Danger 5: Common Pool Blocking**
All parallel streams share `ForkJoinPool.commonPool()`!

### When to Use
✅ Large dataset (1000+ elements)
✅ CPU-intensive operations
✅ No shared mutable state
✅ Splittable data structure (ArrayList)
✅ Order doesn't matter

**Default to sequential. Parallelize only after profiling!**

---

## 24. Design Patterns

### 24.1 Immutable Objects

**Thread-safe by design - never changes!**

```java
final class User {
    private final String name;
    private final int age;
    
    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // Only getters, no setters
    public String getName() { return name; }
    public int getAge() { return age; }
}
```

**Benefits:**
- No synchronization needed
- Safe to share
- Simpler reasoning

**To modify:** Create new instance
```java
User older = new User(user.getName(), user.getAge() + 1);
```

---

### 24.2 Thread Confinement

**If only one thread accesses data, no synchronization needed!**

#### Stack Confinement
```java
public void process() {
    int local = 0;  // Stack-confined
    for (int i = 0; i < 100; i++) {
        local += i;  // Thread-safe!
    }
}
```

#### ThreadLocal
```java
ThreadLocal<SimpleDateFormat> formatter = ThreadLocal.withInitial(
    () -> new SimpleDateFormat("yyyy-MM-dd")
);

// Each thread gets its own instance
formatter.get().format(date);
```

**Warning:** Clean up in thread pools!
```java
try {
    formatter.set(new SimpleDateFormat(...));
    // Use it
} finally {
    formatter.remove();  // Prevent memory leak
}
```

---

### 24.3 Double-Checked Locking

**Lazy initialization with minimal locking**

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

**Why volatile?** Prevents reordering that could expose partially constructed object.

**Better Alternative:** Initialization-on-Demand Holder
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

**Best:** Enum Singleton
```java
enum Singleton {
    INSTANCE;
    
    public void doSomething() {
        // ...
    }
}
```

---

## Part 7: Special Topics

---

## 25. Daemon Threads

### What are Daemon Threads?
Background threads that **support non-daemon threads** (e.g., garbage collector).

### Characteristics
- Run in background
- Provide support services
- **JVM exits when only daemon threads remain**
- Terminated automatically when last non-daemon thread ends

### Methods
```java
boolean isDaemon();          // Check if daemon
void setDaemon(boolean b);   // Set daemon (before start!)
```

### Example
```java
MyThread t = new MyThread();
t.setDaemon(true);  // Must be before start()
t.start();
```

### Important Rules
- **Main thread** is always non-daemon (can't be changed)
- Must call `setDaemon()` **before** `start()`
- Daemon property **inherited** from parent thread

### Example: Auto-Termination
```java
class MyThread extends Thread {
    public void run() {
        for (int i = 0; i < 10; i++) {
            System.out.println("Lazy thread");
            Thread.sleep(2000);
        }
    }
}

MyThread t = new MyThread();
t.setDaemon(true);  // Make daemon
t.start();
System.out.println("End of main");
// JVM exits immediately, daemon may not complete!
```

### When to Use
✅ Background tasks (cleanup, monitoring)
✅ Support services (logging, caching)
✅ Tasks that can be interrupted anytime

❌ Critical tasks that must complete

---

## 26. ThreadGroup & ThreadLocal

### 26.1 ThreadGroup

Group multiple threads for **collective management**.

```java
ThreadGroup group = new ThreadGroup("Worker Threads");

Thread t1 = new Thread(group, () -> work(), "Worker-1");
Thread t2 = new Thread(group, () -> work(), "Worker-2");
Thread t3 = new Thread(group, () -> work(), "Worker-3");

t1.start();
t2.start();
t3.start();

// Manage group
group.interrupt();  // Interrupt all threads
group.setMaxPriority(8);
```

### Methods
```java
int activeCount();           // Active threads in group
void interrupt();            // Interrupt all threads
void setMaxPriority(int p);  // Set max priority
```

**Note:** Rarely used in modern Java. Prefer ExecutorService.

---

### 26.2 ThreadLocal

Provides **thread-local variables** - each thread has its own copy.

```java
ThreadLocal<Integer> threadId = new ThreadLocal<>();

// Thread 1
threadId.set(100);
System.out.println(threadId.get());  // 100

// Thread 2
threadId.set(200);
System.out.println(threadId.get());  // 200
```

### Common Use Cases

**1. Per-thread context**
```java
ThreadLocal<User> currentUser = new ThreadLocal<>();

public void setUser(User user) {
    currentUser.set(user);
}

public User getUser() {
    return currentUser.get();
}
```

**2. Non-thread-safe objects**
```java
ThreadLocal<SimpleDateFormat> formatter = ThreadLocal.withInitial(
    () -> new SimpleDateFormat("yyyy-MM-dd")
);

String formatted = formatter.get().format(date);
```

**3. Database connections**
```java
ThreadLocal<Connection> connection = new ThreadLocal<>();
```

### Memory Leak Warning
```java
// In thread pool, always clean up!
try {
    threadLocal.set(value);
    doWork();
} finally {
    threadLocal.remove();  // IMPORTANT!
}
```

---

## 27. Best Practices

### 27.1 General Guidelines

**1. Prefer High-Level Tools**
```java
// BAD
new Thread(() -> task()).start();

// GOOD
executor.submit(() -> task());
```

**2. Immutability First**
```java
// GOOD: Immutable, thread-safe
final class Config {
    private final String value;
    // No setters
}
```

**3. Minimize Synchronized Scope**
```java
// BAD: Entire method synchronized
public synchronized void method() {
    // Lots of code
}

// GOOD: Only critical section
public void method() {
    // Non-critical code
    synchronized(this) {
        // Only critical section
    }
    // More non-critical code
}
```

**4. Avoid Nested Locks**
```java
// BAD: Potential deadlock
synchronized(obj1) {
    synchronized(obj2) {
        // ...
    }
}

// GOOD: Single lock or lock ordering
```

**5. Always Shutdown Executors**
```java
ExecutorService executor = ...;
try {
    // Use it
} finally {
    executor.shutdown();
    executor.awaitTermination(60, TimeUnit.SECONDS);
}
```

---

### 27.2 Synchronization Guidelines

**Use synchronized when:**
- Simple mutual exclusion
- Short critical sections
- No timeout needed

**Use ReentrantLock when:**
- Need timeout (`tryLock()`)
- Need interruptible lock
- Need fairness
- Need condition variables

**Use ReadWriteLock when:**
- 90%+ reads
- Read operations are expensive

**Use volatile when:**
- Simple flag/status
- One writer, many readers
- No compound operations

**Use AtomicInteger when:**
- Simple counter
- No complex invariants

---

### 27.3 Common Pitfalls

**1. Not Using finally with Locks**
```java
// BAD
lock.lock();
doWork();
lock.unlock();  // Might not execute!

// GOOD
lock.lock();
try {
    doWork();
} finally {
    lock.unlock();
}
```

**2. Using volatile for Compound Operations**
```java
// BAD
private volatile int count = 0;
public void increment() {
    count++;  // NOT atomic!
}

// GOOD
private AtomicInteger count = new AtomicInteger(0);
public void increment() {
    count.incrementAndGet();
}
```

**3. Blocking in Parallel Streams**
```java
// BAD: Blocks common pool
list.parallelStream()
    .forEach(item -> makeNetworkCall(item));

// GOOD: Use CompletableFuture
list.stream()
    .map(item -> CompletableFuture.supplyAsync(() -> makeNetworkCall(item)))
    .collect(Collectors.toList());
```

---

### 27.4 Testing Guidelines

**1. Use Thread Safety Annotations**
```java
@ThreadSafe
public class SafeCounter {
    // ...
}

@NotThreadSafe
public class UnsafeCounter {
    // ...
}
```

**2. Test with Multiple Threads**
```java
@Test
public void testConcurrency() throws Exception {
    CountDownLatch latch = new CountDownLatch(1);
    List<Thread> threads = new ArrayList<>();
    
    for (int i = 0; i < 10; i++) {
        Thread t = new Thread(() -> {
            latch.await();  // All start together
            doWork();
        });
        threads.add(t);
        t.start();
    }
    
    latch.countDown();  // Release all threads
    
    for (Thread t : threads) {
        t.join();
    }
    
    // Assert results
}
```

**3. Use Race Detection Tools**
- Thread Sanitizer
- FindBugs
- Helgrind (Valgrind)

---

### 27.5 Performance Tips

**1. Reduce Lock Contention**
- Minimize synchronized scope
- Use concurrent collections
- Consider lock-free algorithms

**2. Choose Right Tool**
```java
// Sequential small data
list.stream()...

// Parallel large CPU-bound data
list.parallelStream()...

// Async I/O operations
CompletableFuture.supplyAsync()...
```

**3. Avoid Creating Too Many Threads**
```java
// BAD: One thread per request
for (Request req : requests) {
    new Thread(() -> handle(req)).start();
}

// GOOD: Thread pool
ExecutorService executor = Executors.newFixedThreadPool(cores);
for (Request req : requests) {
    executor.submit(() -> handle(req));
}
```

---

## Quick Reference

### Decision Trees

#### Thread Creation
```
Need to extend another class?
├─ Yes → Implement Runnable ✅
└─ No → Either, but Runnable preferred ✅
```

#### Synchronization
```
What do you need?
├─ Simple flag → volatile
├─ Counter → AtomicInteger
├─ Complex operation → synchronized/Lock
└─ Read-heavy → ReadWriteLock
```

#### Thread Pool
```
What kind of tasks?
├─ CPU-bound → newFixedThreadPool(#cores)
├─ I/O-bound → newCachedThreadPool()
├─ Sequential → newSingleThreadExecutor()
└─ Scheduled → newScheduledThreadPool()
```

#### Parallel Processing
```
How to parallelize?
├─ Small data → Don't parallelize
├─ Large CPU-bound → Parallel streams
├─ I/O operations → CompletableFuture
└─ Recursive task → Fork/Join
```

---

### Comparison Tables

#### Lock Release

| Method | Releases Lock? |
|--------|----------------|
| `yield()` | ❌ No |
| `join()` | ❌ No |
| `sleep()` | ❌ No |
| `wait()` | ✅ Yes (immediately) |
| `notify()` | ✅ Yes (after sync block) |

#### Synchronizers

| Tool | Reusable | Dynamic | Use Case |
|------|----------|---------|----------|
| CountDownLatch | ❌ No | ❌ No | Wait for N events |
| CyclicBarrier | ✅ Yes | ❌ No | Sync N threads |
| Phaser | ✅ Yes | ✅ Yes | Multi-phase |
| Semaphore | ✅ Yes | N/A | Limit access |

#### Atomic vs volatile

| Feature | volatile | AtomicInteger |
|---------|----------|---------------|
| Visibility | ✅ | ✅ |
| Simple read/write | ✅ | ✅ |
| Compound operations | ❌ | ✅ |
| CAS | ❌ | ✅ |
| Performance | Better | Good |

---

## Summary

### Core Concepts
1. **Threads** allow concurrent execution
2. **Synchronization** prevents data races
3. **volatile** ensures visibility
4. **Atomic classes** provide lock-free operations
5. **Executors** manage thread pools
6. **Concurrent collections** are thread-safe
7. **CompletableFuture** enables async pipelines

### Golden Rules
1. **Prefer immutability** over synchronization
2. **Use high-level tools** (ExecutorService, not Thread)
3. **Minimize lock scope** (synchronize only what's necessary)
4. **Avoid nested locks** (deadlock risk)
5. **Always use finally** with locks
6. **Test concurrency** thoroughly
7. **Profile before optimizing** (don't assume parallelism helps)

### The Expert Mindset
- **Simple first:** Start with simplest solution
- **Prove need:** Profile before adding complexity
- **Understand tools:** Know when each pattern fits
- **Design carefully:** Best bug is one prevented
- **Test rigorously:** Concurrency bugs are subtle

---

**Remember:** Multithreading is powerful but complex. Master the basics, use proven tools, and always think about thread safety from the start of your design!

---

## Further Reading
- **Java Concurrency in Practice** - Brian Goetz (The Bible)
- **Effective Java** - Joshua Bloch (Items 78-84)
- **Java Language Specification** - Chapter 17 (Memory Model)
- **Oracle Concurrency Tutorial** - docs.oracle.com
