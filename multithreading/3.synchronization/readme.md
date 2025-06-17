**Synchronization in Java**

1. `synchronized` is a keyword applicable for methods and blocks but **not** for classes and variables.
2. If a method or block is declared as `synchronized`, then at a time only **one thread** is allowed to execute that method or block on the given object.
3. **Advantage**: Solves **data inconsistency** problems.
4. **Disadvantage**: Increases **waiting time** for threads and affects **performance**.
5. If there's no specific need, it is **not recommended** to use `synchronized`.
6. Internally, synchronization is implemented using the **lock** concept.
7. Every object in Java has a **unique lock**. Lock comes into the picture only when `synchronized` keyword is used.
8. A thread must acquire the **lock** of an object before executing any synchronized method on it. Once the thread completes, it **releases** the lock.
9. While one thread is executing a synchronized method, **no other thread** can execute any synchronized method on the same object. However, other threads **can** execute non-synchronized methods.

### Example:

```java
class Display {
    public synchronized void wish(String name) {
        for(int i = 0; i < 5; i++) {
            System.out.print("good morning:");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {}
            System.out.println(name);
        }
    }
}

class MyThread extends Thread {
    Display d;
    String name;

    MyThread(Display d, String name) {
        this.d = d;
        this.name = name;
    }

    public void run() {
        d.wish(name);
    }
}

public class SynchronizedDemo {
    public static void main(String[] args) {
        Display d1 = new Display();
        MyThread t1 = new MyThread(d1, "dhoni");
        MyThread t2 = new MyThread(d1, "yuvaraj");
        t1.start();
        t2.start();
    }
}
```

### Without `synchronized` keyword:

**Output:** (irregular due to context switching)

```
good morning:good morning:yuvaraj
good morning:dhoni
good morning:yuvaraj
good morning:dhoni
...
```

### With `synchronized` keyword:

**Output:** (regular and consistent)

```
good morning:dhoni
good morning:dhoni
...
good morning:yuvaraj
good morning:yuvaraj
```

### Case Study:

```java
Display d1 = new Display();
Display d2 = new Display();
MyThread t1 = new MyThread(d1, "dhoni");
MyThread t2 = new MyThread(d2, "yuvaraj");
t1.start();
t2.start();
```

Even though `wish()` is synchronized, output will be **irregular** because both threads are using **different Display objects**.

**Conclusion**:

* Synchronization is **effective** only when multiple threads operate on the **same object**.
* If threads operate on **different objects**, synchronization **doesn't apply**.

**Class Level Lock**

1. Every class in Java has a **unique lock**.
2. If a thread wants to execute a **static synchronized method**, it needs to acquire the **class level lock**.
3. Once a thread acquires the class level lock, it can execute **any static synchronized method** of that class.
4. While a thread is executing a static synchronized method, other threads **cannot** execute **any other static synchronized method** of the same class.
5. However, other threads **can** execute:

   * Normal synchronized methods
   * Normal static methods
   * Normal instance methods
6. **Class level lock and object lock are different** and have no relationship between them.

---

**Synchronized Block**

1. If **only a few lines** of code require synchronization, it's **not recommended** to declare the entire method as synchronized.
2. Instead, enclose only the required lines in a **synchronized block**.
3. **Advantage**: Reduces thread waiting time and **improves performance**.

---

**Examples**

**Example 1**: Locking the current object

```java
synchronized(this) {
    // critical section code
}
```

* The thread must acquire the **lock of the current object** to execute this block.

**Example 2**: Locking a specific object `b`

```java
synchronized(b) {
    // critical section code
}
```

* The thread must acquire the **lock of object `b`** to execute this block.

**Example 3**: Locking at class level

```java
synchronized(Display.class) {
    // critical section code
}
```

* The thread must acquire the **class level lock** of `Display` to execute this block.

**Note**:

* The argument to a synchronized block must be an **object reference** or a **class literal (e.g., ClassName.class)**.
* **Primitive values cannot be used** as arguments because locking is applicable only to **objects or classes**, not primitives.

**Invalid Example**:

```java
int x = 10;
synchronized(x) {
    // Compile-time error
}
```

**Output**:

```
Compile-time error: Unexpected type.
Found: int
Required: reference
```
