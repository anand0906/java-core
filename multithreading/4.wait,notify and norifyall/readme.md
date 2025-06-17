**Inter Thread Communication (wait(), notify(), notifyAll()):**

* Two Threads can communicate with each other using `wait()`, `notify()` and `notifyAll()` methods.
* The thread that needs an update should call `wait()` on the required object, which moves it into the waiting state.
* The thread that performs the update should call `notify()` to give a notification. After receiving the notification, the waiting thread will retrieve the updates.

**Why these methods are in Object class?**

* `wait()`, `notify()`, and `notifyAll()` are available in the `Object` class (not in the `Thread` class) because threads call these methods on shared objects.

**Conditions to call these methods:**

* The current thread must own the lock of the object (i.e., it must be inside a synchronized block or method).
* If called outside a synchronized area, a `RuntimeException` (`IllegalMonitorStateException`) is thrown.

**Lock Release Behavior:**

* `wait()` — Immediately releases the lock of the object and enters the waiting state.
* `notify()`/`notifyAll()` — Releases the lock, but **not immediately**, only after the synchronized block ends.
* Only `wait()`, `notify()`, and `notifyAll()` cause lock release. Others like `yield()`, `sleep()`, `join()` **do not** release the lock.

<img src="https://miro.medium.com/v2/resize:fit:4800/format:webp/1*Y6-pb42dmp3hCPGbtXoZIA.png">

| Method        | Releases Lock?        |
| ------------- | --------------------- |
| `yield()`     | No                    |
| `join()`      | No                    |
| `sleep()`     | No                    |
| `wait()`      | Yes                   |
| `notify()`    | Yes (not immediately) |
| `notifyAll()` | Yes (not immediately) |

* Calling these methods only releases the lock of the specific object, **not all locks** held by the thread.

**Method Signatures:**

```java
public final void wait() throws InterruptedException
public final native void wait(long ms) throws InterruptedException
public final void wait(long ms, int ns) throws InterruptedException
public final native void notify()
public final void notifyAll()
```

**Example 1: Basic Wait and Notify**

```java
class ThreadA {
  public static void main(String[] args) throws InterruptedException {
    ThreadB b = new ThreadB();
    b.start();
    synchronized(b) {
      System.out.println("main Thread calling wait() method"); // step-1
      b.wait();
      System.out.println("main Thread got notification call"); // step-4
      System.out.println(b.total);
    }
  }
}

class ThreadB extends Thread {
  int total = 0;
  public void run() {
    synchronized(this) {
      System.out.println("child thread starts calculation"); // step-2
      for (int i = 0; i <= 100; i++) {
        total += i;
      }
      System.out.println("child thread giving notification call"); // step-3
      this.notify();
    }
  }
}
```

**Output:**

```
main Thread calling wait() method
child thread starts calculation
child thread giving notification call
main Thread got notification call
5050
```

**Example 2: Producer-Consumer Problem**

* Producer thread adds items to the queue.
* Consumer thread removes items from the queue.
* If the queue is empty, the consumer calls `wait()`.
* After producing, the producer calls `notify()` to wake up the waiting consumer.

**notify() vs notifyAll():**

* `notify()` wakes up **one** waiting thread (which one is picked is up to JVM).
* `notifyAll()` wakes up **all** waiting threads. They acquire the lock one by one and execute.

**Important Note:**

* The lock must be acquired on **the same object** on which `wait()`, `notify()`, and `notifyAll()` are called.

**True/False Questions:**

1. Once a Thread calls `wait()` on any Object, it enters the waiting state without releasing the lock? — **NO**
2. Once a Thread calls `wait()` on any Object, it releases the lock of that Object but may not immediately? — **NO**
3. Once a Thread calls `wait()` on any Object, it immediately releases all locks it has? — **NO**
4. Once a Thread calls `wait()` on any Object, it immediately releases the lock of **that particular** object and enters the waiting state? — **YES**
5. Once a Thread calls `notify()` on any Object, it immediately releases the lock of that Object? — **NO**
6. Once a Thread calls `notify()` on any Object, it releases the lock of that Object but may not immediately? — **YES**


**Deadlock**

* If two threads are waiting for each other forever (without end), such a situation is called a **deadlock**.
* There are no resolution techniques for deadlock, but several **prevention** or **avoidance** techniques are possible.
* The `synchronized` keyword is the primary cause of deadlock. Hence, special care should be taken while using it.

**Example:**

```java
class A {
    public synchronized void foo(B b) {
        System.out.println("Thread1 starts execution of foo() method");
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {}
        System.out.println("Thread1 trying to call b.last()");
        b.last();
    }

    public synchronized void last() {
        System.out.println("Inside A, this is last() method");
    }
}

class B {
    public synchronized void bar(A a) {
        System.out.println("Thread2 starts execution of bar() method");
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {}
        System.out.println("Thread2 trying to call a.last()");
        a.last();
    }

    public synchronized void last() {
        System.out.println("Inside B, this is last() method");
    }
}

class DeadLock implements Runnable {
    A a = new A();
    B b = new B();

    DeadLock() {
        Thread t = new Thread(this);
        t.start();
        a.foo(b); // main thread
    }

    public void run() {
        b.bar(a); // child thread
    }

    public static void main(String[] args) {
        new DeadLock();
    }
}
```

**Output:**

```
Thread1 starts execution of foo() method
Thread2 starts execution of bar() method
Thread2 trying to call a.last()
Thread1 trying to call b.last()
```

(Then, the cursor always waits — deadlock occurs)

**Note:** If we remove even one `synchronized` keyword, the deadlock won't occur.

---

**Daemon Threads**

* Daemon threads run in the background and provide support for non-daemon threads (e.g., garbage collector).

**Methods:**

* `isDaemon()` – checks if the thread is daemon.
* `setDaemon(boolean b)` – sets a thread as daemon. Must be set **before** starting the thread.

**Important Points:**

* Main thread is always non-daemon and cannot be made daemon.
* Daemon property is inherited from parent to child.
* When the last non-daemon thread terminates, all daemon threads terminate automatically.

**Example 1:**

```java
class MyThread extends Thread {}

class DaemonThreadDemo {
    public static void main(String[] args) {
        System.out.println(Thread.currentThread().isDaemon());
        MyThread t = new MyThread();
        System.out.println(t.isDaemon());
        t.start();
        t.setDaemon(true); // Throws IllegalThreadStateException
        System.out.println(t.isDaemon());
    }
}
```

**Output:**

```
false
false
Exception in thread "main" java.lang.IllegalThreadStateException
```

**Example 2:**

```java
class MyThread extends Thread {
    public void run() {
        for (int i = 0; i < 10; i++) {
            System.out.println("lazy thread");
            try {
                Thread.sleep(2000);
            } catch (InterruptedException e) {}
        }
    }
}

class DaemonThreadDemo {
    public static void main(String[] args) {
        MyThread t = new MyThread();
        t.setDaemon(true); // Make it daemon before start
        t.start();
        System.out.println("end of main Thread");
    }
}
```

**Output:**

```
end of main Thread
```

(Child thread may or may not complete depending on JVM scheduling)

---

**Deadlock vs Starvation**

| Feature    | Deadlock             | Starvation                                |
| ---------- | -------------------- | ----------------------------------------- |
| Definition | Threads wait forever | Thread waits long but eventually proceeds |
| Cause      | Mutual lock waiting  | Thread priority/indefinite postponement   |
| Ends?      | No                   | Eventually yes                            |

---

**Thread Control Methods (Deprecated)**

* `stop()` – forcibly kills a thread (Deprecated)
* `suspend()` – pauses a thread (Deprecated)
* `resume()` – resumes a suspended thread (Deprecated)

**Race Condition**

* Occurs when multiple threads access shared data simultaneously causing inconsistency.
* Prevented using the `synchronized` keyword.

---

**ThreadGroup**

* Groups multiple threads for collective management.

```java
ThreadGroup g = new ThreadGroup("Printing Threads");
MyThread t1 = new MyThread(g, "Header Printing");
MyThread t2 = new MyThread(g, "Footer Printing");
MyThread t3 = new MyThread(g, "Body Printing");
```

**ThreadGroup Constructor:**

```java
ThreadGroup g = new ThreadGroup(String gName);
Thread t = new Thread(ThreadGroup g, String name);
g.stop();
```

---

**ThreadLocal (Java 1.2)**

* Provides thread-local variables (e.g., DB connection, counters).
* Useful in multi-threaded environments (e.g., servlets).

---

**Green Threads vs Native Threads**

* **Green Threads**: Managed entirely by JVM (Deprecated)
* **Native Threads**: Managed by the OS

  * Windows uses native threads
  * Sun Solaris supported green threads (no longer recommended)

