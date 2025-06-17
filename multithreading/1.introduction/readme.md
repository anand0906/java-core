**Introduction to Multitasking and Multithreading in Java**

---

### Multitasking

Multitasking refers to executing several tasks simultaneously. There are two types of multitasking:

#### 1. Process-Based Multitasking

Executing several tasks simultaneously where each task is a separate independent process.

**Example:**

* Typing a Java program in the editor
* Listening to MP3 audio songs
* Downloading a file from the internet

These tasks are independent and execute simultaneously.

* **Best suitable at OS level**

#### 2. Thread-Based Multitasking

Executing several tasks simultaneously where each task is an independent part of the same program. Each independent part is called a "Thread".

* **Best suitable at programmatic level**
* Easier to develop in Java compared to C++ due to Java's built-in multithreading support via rich APIs like `Thread`, `Runnable`, `ThreadGroup`, `ThreadLocal`, etc.
* In multithreading, 90% of the work is handled by Java API and only 10% by the programmer

**Application areas:**

1. Multimedia graphics
2. Video games
3. Web and application servers

> Whether process-based or thread-based, the main objective of multitasking is to improve system performance by reducing response time.

---

### Defining, Instantiating, and Starting a Thread

Threads in Java can be defined in two ways:

1. By extending the `Thread` class
2. By implementing the `Runnable` interface

#### Defining a Thread by Extending the Thread Class

```java
class MyThread extends Thread {
   public void run() {
        for (int i = 0; i < 10; i++)
            System.out.println("child thread");
   }
}

class ThreadDemo {
   public static void main(String[] args) {
      MyThread t = new MyThread(); // Instantiation
      t.start(); // Starting the thread

      for (int i = 0; i < 5; i++) {
         System.out.println("main thread");
      }
   }
}
```

---

### Important Cases and Concepts

#### Case 1: Thread Scheduler

* Decides which thread executes first when multiple threads are waiting.
* It is part of JVM and behavior is JVM vendor dependent.
* Hence, output is non-deterministic.

#### Case 2: Difference between `t.start()` and `t.run()`

* `t.start()` creates a new thread and executes `run()` method in that thread.
* `t.run()` executes like a normal method in the main thread.

\*\*Output with ****`t.run()`**** instead of \*\***`t.start()`**

```
child thread (10 times)
main thread (5 times)
```

> Entire output is produced by the main thread.

#### Case 3: Importance of `start()` Method

The `start()` method:

1. Registers the thread with the Thread Scheduler
2. Handles other mandatory low-level activities
3. Calls the `run()` method

> `start()` is considered the heart of multithreading.

#### Case 4: Not Overriding `run()` Method

```java
class MyThread extends Thread {}

class ThreadDemo {
   public static void main(String[] args) {
      MyThread t = new MyThread();
      t.start();
   }
}
```

* No output since `Thread` class `run()` method has empty implementation
* Always recommended to override `run()` method

#### Case 5: Overloading `run()` Method

```java
class MyThread extends Thread {
   public void run() {
      System.out.println("no arg method");
   }
   public void run(int i) {
      System.out.println("int arg method");
   }
}

class ThreadDemo {
   public static void main(String[] args) {
      MyThread t = new MyThread();
      t.start();
   }
}
```

**Output:**

```
no arg method
```

> Only no-arg `run()` is called by `start()`; others must be called explicitly.

#### Case 6: Overriding `start()` Method

```java
class MyThread extends Thread {
   public void start() {
      System.out.println("start method");
   }
   public void run() {
      System.out.println("run method");
   }
}

class ThreadDemo {
   public static void main(String[] args) {
      MyThread t = new MyThread();
      t.start();
      System.out.println("main method");
   }
}
```

**Output:**

```
start method
main method
```

> No new thread is started. Avoid overriding `start()` method.

#### Case 7: Life Cycle of a Thread

**States:**

1. **New/Born State** – After creating thread object
2. **Runnable State** – After calling `start()`
3. **Running State** – When CPU is allocated
4. **Dead State** – After completion of `run()`

<img src="https://javatrainingschool.com/wp-content/uploads/2021/09/image-13-1536x715.png">

#### Case 9: Restarting a Thread

```java
MyThread t = new MyThread();
t.start(); // Valid
...
t.start(); // RuntimeException: IllegalThreadStateException
```

> Once a thread is started, it cannot be restarted.

---

**Defining a Thread by Implementing Runnable Interface**

We can define a thread by implementing the `Runnable` interface. The `Runnable` interface is part of the `java.lang` package and contains only one method: `run()`.

```java
class MyRunnable implements Runnable {
   public void run() {
       for (int i = 0; i < 10; i++) {
            System.out.println("child Thread");
       }
   }
}

class ThreadDemo {
   public static void main(String[] args) {
      MyRunnable r = new MyRunnable();
      Thread t = new Thread(r); // r is a Target Runnable
      t.start();

      for (int i = 0; i < 10; i++) {
         System.out.println("main thread");
      }
   }
}
```

### Output (Possible Outputs)

```
main thread
main thread
...
child Thread
child Thread
...
```

We can't expect exact output due to thread scheduling. There are several possible outputs.

---

### Case Study

```java
MyRunnable r = new MyRunnable();
Thread t1 = new Thread();
Thread t2 = new Thread(r);
```

#### Case 1: `t1.start()`

* A new thread will be created.
* Executes `Thread` class `run()` method (empty).
* **Output:**

```
main thread
main thread
...
```

#### Case 2: `t1.run()`

* No new thread is created.
* `Thread` class `run()` method executed like normal method.
* **Output:**

```
main thread
main thread
...
```

#### Case 3: `t2.start()`

* New thread will be created.
* Executes `MyRunnable`'s `run()` method.
* **Output:**

```
main thread
main thread
...
child Thread
child Thread
...
```

#### Case 4: `t2.run()`

* No new thread is created.
* `MyRunnable`'s `run()` method executed like normal method.
* **Output:**

```
child Thread
child Thread
...
main thread
main thread
...
```

#### Case 5: `r.start()`

* Compile-time error: `start()` method not available in `MyRunnable` class.
* **Output:**

```
Compile time error:
ThreadDemo.java:18: cannot find symbol
Symbol: method start()
Location: class MyRunnable
```

#### Case 6: `r.run()`

* No new thread is created.
* `MyRunnable`'s `run()` method executed like a normal method.
* **Output:**

```
child Thread
child Thread
...
main thread
main thread
...
```

---

### Summary of Thread Creation

* **New Thread created and executes `MyRunnable`'s `run()` method:** `t2.start();`
* **New Thread created:** `t1.start();`, `t2.start();`
* **`MyRunnable`'s `run()` executed:** `t2.start();`, `t2.run();`, `r.run();`

---

### Best Approach to Define a Thread

* Implementing `Runnable` is always recommended.
* **Why?**

  * Extending `Thread` restricts you from extending any other class.
  * Implementing `Runnable` allows extending other classes as well.
* **Conclusion:**

  * **Preferred approach:** `implements Runnable`

---

### Thread Class Constructors

1. `Thread t = new Thread();`
2. `Thread t = new Thread(Runnable r);`
3. `Thread t = new Thread(String name);`
4. `Thread t = new Thread(Runnable r, String name);`
5. `Thread t = new Thread(ThreadGroup g, String name);`
6. `Thread t = new Thread(ThreadGroup g, Runnable r);`
7. `Thread t = new Thread(ThreadGroup g, Runnable r, String name);`
8. `Thread t = new Thread(ThreadGroup g, Runnable r, String name, long stackSize);`

---

### Getting and Setting Name of a Thread

* Every Thread in Java has a name, either set explicitly or generated by JVM.
* Thread class provides the following methods:

**Methods:**

1. `public final String getName()`
2. `public final void setName(String name)`

**Example:**

```java
class MyThread extends Thread {}

class ThreadDemo {
    public static void main(String[] args) {
        System.out.println(Thread.currentThread().getName()); // main
        MyThread t = new MyThread();
        System.out.println(t.getName()); // Thread-0

        Thread.currentThread().setName("Bhaskar Thread");
        System.out.println(Thread.currentThread().getName()); // Bhaskar Thread
    }
}
```

> `Thread.currentThread()` returns the current executing Thread reference.

---

### Thread Priorities

* Each Thread in Java has a priority: default or explicitly set.
* Valid range: **1 to 10** (not 0 to 10)

**Thread Priority Constants:**

1. `Thread.MIN_PRIORITY` = 1
2. `Thread.MAX_PRIORITY` = 10
3. `Thread.NORM_PRIORITY` = 5

> No constants like `Thread.LOW_PRIORITY` or `Thread.HIGH_PRIORITY`.

* Thread Scheduler uses these priorities to determine thread execution order.
* Threads with the same priority may not have deterministic order—scheduler dependent.

**Methods:**

1. `public final int getPriority()`
2. `public final void setPriority(int newPriority)`

> Values outside 1 to 10 throw `IllegalArgumentException`

---

### Default Priority Behavior

* Main thread has a default priority of 5.
* Other threads inherit their parent's priority.

**Example 1:**

```java
class MyThread extends Thread {}

class ThreadPriorityDemo {
    public static void main(String[] args) {
        System.out.println(Thread.currentThread().getPriority()); // 5
        Thread.currentThread().setPriority(9);
        MyThread t = new MyThread();
        System.out.println(t.getPriority()); // 9
    }
}
```

**Example 2:**

```java
class MyThread extends Thread {
    public void run() {
        for (int i = 0; i < 10; i++) {
            System.out.println("child thread");
        }
    }
}

class ThreadPriorityDemo {
    public static void main(String[] args) {
        MyThread t = new MyThread();
        // t.setPriority(10); // Uncomment to set high priority
        t.start();

        for (int i = 0; i < 10; i++) {
            System.out.println("main thread");
        }
    }
}
```

**Output without setting priority:** Unpredictable

**Output with priority set:**

```
child thread
child thread
...
main thread
main thread
...
```

> Some OS (e.g., Windows XP) might not support Thread priorities correctly. Vendor-specific patches might be needed.

