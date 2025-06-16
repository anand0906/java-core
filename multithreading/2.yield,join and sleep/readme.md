# Java Thread Control Methods in Java

Java provides several key methods to control the execution behavior of threads. These methods are essential to manage thread lifecycle and synchronization:

---

## 1. `yield()` Method

**Purpose:** Pause current thread to allow other waiting threads of same priority to execute.

**Key Points:**

* Belongs to `Thread` class.
* Static and native method: `public static native void yield();`
* May not guarantee the pause; depends on Thread Scheduler.
* If no thread of same priority is waiting, the same thread may continue.

```java
class MyThread extends Thread {
  public void run() {
    for (int i = 0; i < 5; i++) {
      Thread.yield();
      System.out.println("Child Thread");
    }
  }
}

public class ThreadYieldDemo {
  public static void main(String[] args) {
    MyThread t = new MyThread();
    t.start();
    for (int i = 0; i < 5; i++) {
      System.out.println("Main Thread");
    }
  }
}
```

**Expected Output:**

```
Main Thread
Main Thread
Main Thread
Main Thread
Main Thread
Child Thread
Child Thread
Child Thread
Child Thread
Child Thread
```

*Note: OS-dependent behavior, not always consistent.*

---

## 2. `join()` Method

**Purpose:** Pause current thread execution until another thread finishes its task.

**Methods:**

```java
public final void join() throws InterruptedException
public final void join(long ms) throws InterruptedException
public final void join(long ms, int ns) throws InterruptedException
```

**Example 1:**

```java
class MyThread extends Thread {
  public void run() {
    for (int i = 0; i < 5; i++) {
      System.out.println("Sita Thread");
      try {
        Thread.sleep(2000);
      } catch (InterruptedException e) {}
    }
  }
}

public class ThreadJoinDemo {
  public static void main(String[] args) throws InterruptedException {
    MyThread t = new MyThread();
    t.start();
    // t.join(); // Uncomment to enforce main thread wait
    for (int i = 0; i < 5; i++) {
      System.out.println("Rama Thread");
    }
  }
}
```

**Behavior:**

* If `t.join()` is uncommented, child thread completes first.
* If commented, both threads run simultaneously.

**Example 2 (Main waits for Child):**

```java
class MyThread extends Thread {
  static Thread mainThread;
  public void run() {
    try {
      mainThread.join();
    } catch (InterruptedException e) {}
    for (int i = 0; i < 5; i++) {
      System.out.println("Child Thread");
    }
  }
}

public class ThreadJoinDemo2 {
  public static void main(String[] args) throws InterruptedException {
    MyThread.mainThread = Thread.currentThread();
    MyThread t = new MyThread();
    t.start();
    for (int i = 0; i < 5; i++) {
      Thread.sleep(2000);
      System.out.println("Main Thread");
    }
  }
}
```

---

## 3. `sleep()` Method

**Purpose:** Pause the thread for a specified time.

**Methods:**

```java
public static native void sleep(long ms) throws InterruptedException
public static void sleep(long ms, int ns) throws InterruptedException
```

**Example:**

```java
public class ThreadSleepDemo {
  public static void main(String[] args) throws InterruptedException {
    System.out.println("M");
    Thread.sleep(3000);
    System.out.println("E");
    Thread.sleep(3000);
    System.out.println("G");
    Thread.sleep(3000);
    System.out.println("A");
  }
}
```

**Output:**

```
M
(wait 3 sec)
E
(wait 3 sec)
G
(wait 3 sec)
A
```

---

## 4. `interrupt()` Method

**Purpose:** Interrupts a sleeping or waiting thread.

**Method:**

```java
public void interrupt();
```

**Example 1:**

```java
class MyThread extends Thread {
  public void run() {
    try {
      for (int i = 0; i < 5; i++) {
        System.out.println("I'm lazy thread: " + i);
        Thread.sleep(2000);
      }
    } catch (InterruptedException e) {
      System.out.println("I got interrupted");
    }
  }
}

public class ThreadInterruptDemo {
  public static void main(String[] args) {
    MyThread t = new MyThread();
    t.start();
    t.interrupt();
    System.out.println("End of main thread");
  }
}
```

**Example 2:** Interrupt after entering sleep:

```java
class MyThread extends Thread {
  public void run() {
    for (int i = 0; i < 5; i++) {
      System.out.println("I'm lazy thread");
    }
    System.out.println("I'm entering into sleep stage");
    try {
      Thread.sleep(3000);
    } catch (InterruptedException e) {
      System.out.println("I got interrupted");
    }
  }
}

public class ThreadInterruptDemo2 {
  public static void main(String[] args) {
    MyThread t = new MyThread();
    t.start();
    t.interrupt();
    System.out.println("End of main thread");
  }
}
```

---

## Comparison of yield(), join(), and sleep()

| Property                     | `yield()`                                           | `join()`                            | `sleep()`                              |
| ---------------------------- | --------------------------------------------------- | ----------------------------------- | -------------------------------------- |
| Purpose                      | Pause current thread to allow same priority threads | Wait for another thread to complete | Pause current thread for specific time |
| Static                       | Yes                                                 | No                                  | Yes                                    |
| Final                        | No                                                  | Yes                                 | No                                     |
| Overloaded                   | No                                                  | Yes                                 | Yes                                    |
| Throws InterruptedException? | No                                                  | Yes                                 | Yes                                    |
| Native                       | Yes                                                 | No                                  | Yes (only `sleep(long)` variant)       |

---

## Diagram: Thread Control Methods

```
+-----------------------------+
|         Thread              |
+-----------------------------+
| yield()    --> pause current execution
| join()     --> wait for another thread
| sleep()    --> pause for specified time
| interrupt() --> break sleep/wait of thread
+-----------------------------+
```

Let me know if you want visuals generated as images.
