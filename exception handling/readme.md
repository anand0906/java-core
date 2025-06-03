**What is Exception and Exception Handling?**

* An unwanted unexpected event that disturbs the normal flow of the program is known as **exception**.
* **Example:** `FileNotFoundException`
* When these exceptions occur, the process of providing an alternative way to continue the rest of the program to execute normally is known as **exception handling**.
* **Example:** Suppose we are reading a file from a remote location. At the time of execution, if the file is not available, then we can tell the program to read a local file.

**What is Error?**

* An unwanted unexpected event that disturbs the normal flow of the program, which can't be handled, is called an **error**.
* **Example:** `OutOfMemoryError`
* If `OutOfMemoryError` occurs, being a programmer we can't do anything; the program will be terminated abnormally. A System Admin or Server Admin is responsible to raise/increase heap memory.

**What is Checked and Unchecked Exception?**

* The exceptions which are checked by the compiler, whether the programmer handles them or not, for smooth execution of the program at runtime are called **Checked Exceptions**.

  * **Example:** `FileNotFoundException`
* The exceptions which are not checked by the compiler, whether the programmer handles them or not, are called **Unchecked Exceptions**.

  * **Example:** `NullPointerException`, `ArithmeticException`

**What is Fully Checked and Partially Checked Exception?**

* A checked exception is said to be **fully checked** if and only if all of its child classes are also checked.

  * **Example:** `IOException`, `InterruptedException`
* A checked exception is said to be **partially checked** if and only if some of its child classes are unchecked.

  * **Example:** `Throwable`, `Exception`

**What is Finally Block?**

* `finally` is the block always associated with try-catch to maintain clean-up code which should be executed always irrespective of whether an exception is raised or not, and whether handled or not.

**Difference between final vs finally vs finalize**

* **final:**

  * A modifier applicable for classes, methods, and variables.
  * If a class is declared as final, then child class creation is not possible.
  * If a method is declared as final, then overriding of that method is not possible.
  * If a variable is declared as final, then reassignment is not possible.
* **finally:**

  * A block always associated with try-catch to maintain clean-up code which should be executed always.
* **finalize:**

  * A method, always invoked by Garbage Collector just before destroying an object to perform clean-up activities.

**What is the output for the following programs with explanation?**

1.

```java
class Test {
  public static void main(String[] args) {
    try {
      System.out.println(10/0);
    }
    catch(Exception e) {
      e.printStackTrace();
    }
    catch(ArithmeticException e) {
      e.printStackTrace();
    }
  }
}
```

**Output:** Compilation Error. Because more general `Exception` must be caught after the more specific `ArithmeticException`.

2.

```java
class Test {
  public static void main(String[] args) {
    try {
      System.out.println("try block executed");
    } catch(ArithmeticException e) {
      System.out.println("catch block executed");
    } finally {
      System.out.println("finally block executed");
    }
  }
}
```

**Output:**

```
try block executed
finally block executed
```

3.

```java
class Test {
  public static void main(String[] args) {
    try {
      System.out.println("try block executed");
      System.out.println(10/0);
    } catch(ArithmeticException e) {
      System.out.println("catch block executed");
    } finally {
      System.out.println("finally block executed");
    }
  }
}
```

**Output:**

```
try block executed
catch block executed
finally block executed
```

4.

```java
class Test {
  public static void main(String[] args) {
    try {
      System.out.println("try block executed");
      System.out.println(10/0);
    } catch(NullPointerException e) {
      System.out.println("catch block executed");
    } finally {
      System.out.println("finally block executed");
    }
  }
}
```

**Output:**

```
try block executed
finally block executed
```

**And program terminates with `ArithmeticException`** (not caught).

5.

```java
class Test {
  public static void main(String[] args) {
    try {
      System.out.println("try block executed");
      return;
    } catch(ArithmeticException e) {
      System.out.println("catch block executed");
    } finally {
      System.out.println("finally block executed");
    }
  }
}
```

**Output:**

```
try block executed
finally block executed
```

6.

```java
class Test {
  public static void main(String[] args) {
    System.out.println(m1());
  }
  public static int m1(){
    try {
      System.out.println(10/0);
      return 777;
    } catch(ArithmeticException e) {
      return 888;
    } finally {
      return 999;
    }
  }
}
```

**Output:** `999`
**Explanation:** Even though `catch` returns 888, `finally` overrides it.

7.

```java
class Test {
  public static void main(String[] args) {
    try {
      System.out.println("try");
      System.exit(0);
    } catch(ArithmeticException e) {
      System.out.println("catch block executed");
    } finally {
      System.out.println("finally block executed");
    }
  }
}
```

**Output:**

```
try
```

**Explanation:** `System.exit(0)` terminates the JVM, so `finally` is not executed.

**What is throw and throws?**

* `throw`: A keyword used to create an exception object manually and hand it over to JVM. Mainly used to raise a custom exception.
* `throws`: A keyword used to delegate the responsibility of exception handling to the caller method. It satisfies the compiler for checked exceptions.

**What is Exception Propagation?**

* If an exception occurs inside a method and it is not handled there, it propagates to the caller method, which must handle it. This process is known as **Exception Propagation**.
