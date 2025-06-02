**Throw Statement in Java**

Sometimes we can create an Exception object explicitly and hand it over to the JVM manually using the `throw` keyword.

The result of the following two programs is exactly the same:

```java
class Test {
    public static void main(String[] args) {
        System.out.println(10/0);
    }
}
```

In this case, creation of `ArithmeticException` object and handover to the JVM is performed automatically by the `main()` method.

```java
class Test {
    public static void main(String[] args) {
        throw new ArithmeticException("/ by zero");
    }
}
```

In this case, we are creating the exception object explicitly and handing it over to the JVM manually.

In general, we can use the `throw` keyword for customized exceptions, but not for predefined exceptions.

---

### Case 1: Null Reference with `throw`

If `e` refers to `null`, then we will get a `NullPointerException`.

**Example 1:**

```java
class Test3 {
    static ArithmeticException e = new ArithmeticException();
    public static void main(String[] args) {
        throw e;
    }
}
```

**Output:**

```
Exception in thread "main" java.lang.ArithmeticException
```

**Example 2:**

```java
class Test3 {
    static ArithmeticException e;
    public static void main(String[] args) {
        throw e;
    }
}
```

**Output:**

```
Exception in thread "main" java.lang.NullPointerException
        at Test3.main(Test3.java:5)
```

---

### Case 2: Unreachable Statement After `throw`

After a `throw` statement, we cannot take any other statement directly; otherwise, we will get a compile-time error saying "unreachable statement".

**Example 1:**

```java
class Test3 {
    public static void main(String[] args) {
        System.out.println(10/0);
        System.out.println("hello");
    }
}
```

**Output:**

```
Exception in thread "main" java.lang.ArithmeticException: / by zero
        at Test3.main(Test3.java:4)
```

**Example 2:**

```java
class Test3 {
    public static void main(String[] args) {
        throw new ArithmeticException("/ by zero");
        System.out.println("hello");
    }
}
```

**Output:**

```
Compile time error:
Test3.java:5: unreachable statement
System.out.println("hello");
```

---

### Case 3: `throw` with Non-Throwable Types

We can use the `throw` keyword only for `Throwable` types; otherwise, we get a compile-time error saying incompatible types.

**Invalid Example:**

```java
class Test3 {
    public static void main(String[] args) {
        throw new Test3();
    }
}
```

**Output:**

```
Compile time error:
Test3.java:4: incompatible types
found   : Test3
required: java.lang.Throwable
```

**Valid Example with Custom Exception:**

```java
class Test3 extends RuntimeException {
    public static void main(String[] args) {
        throw new Test3();
    }
}
```

**Output:**

```
Exception in thread "main" Test3
        at Test3.main(Test3.java:4)
```

---

## Throws Statement in Java

If there is any chance of a checked exception occurring in a Java program, it must be either handled using a `try-catch` block or declared using the `throws` keyword. Failing to do so results in a compilation error.

---

## Basic Examples

### Example 1

```java
import java.io.*;
class Test3 {
    public static void main(String[] args) {
        PrintWriter out = new PrintWriter("abc.txt");
        out.println("hello");
    }
}
```

**Compilation Error:**

```
Unreported exception java.io.FileNotFoundException; must be caught or declared to be thrown.
```

### Example 2

```java
class Test3 {
    public static void main(String[] args) {
        Thread.sleep(5000);
    }
}
```

**Compilation Error:**

```
Unreported exception java.lang.InterruptedException; must be caught or declared to be thrown.
```

---

## Handling Compile-Time Errors

### 1. Using try-catch

```java
class Test3 {
    public static void main(String[] args) {
        try {
            Thread.sleep(5000);
        } catch (InterruptedException e) {}
    }
}
```

**Output:** Compiles and runs successfully.

### 2. Using throws keyword

```java
class Test3 {
    public static void main(String[] args) throws InterruptedException {
        Thread.sleep(5000);
    }
}
```

**Output:** Compiles and runs successfully.

---

## Notes on `throws`

* The main objective of the `throws` keyword is to delegate the responsibility of exception handling to the caller method.
* The `throws` keyword is required only for **checked exceptions**. It has no effect for unchecked exceptions.
* The `throws` keyword is only to satisfy the compiler and does not prevent abnormal program termination.
* It is generally recommended to use `try-catch` over `throws` for robust error handling.

### Example: Delegation Using `throws`

```java
class Test {
    public static void main(String[] args) throws InterruptedException {
        doStuff();
    }
    public static void doStuff() throws InterruptedException {
        doMoreStuff();
    }
    public static void doMoreStuff() throws InterruptedException {
        Thread.sleep(5000);
    }
}
```

**Output:** Compiles and runs successfully.

> If any `throws` declaration is removed in the call chain, the code will not compile.

---

## Special Cases

### Case 1: Only Throwable Types Can Be Thrown

```java
class Test3 {
    public static void main(String[] args) throws Test3 {}
}
```

**Error:**

```
Test3.java:2: incompatible types
found   : Test3
required: java.lang.Throwable
```

### Corrected Version

```java
class Test3 extends RuntimeException {
    public static void main(String[] args) throws Test3 {}
}
```

**Output:** Compiles and runs successfully.

---

### Case 2: Throwing Exceptions

```java
class Test3 {
    public static void main(String[] args) {
        throw new Exception();
    }
}
```

**Error:**

```
Test3.java:3: unreported exception java.lang.Exception; must be caught or declared to be thrown
```

```java
class Test3 {
    public static void main(String[] args) {
        throw new Error();
    }
}
```

**Runtime Output:**

```
Exception in thread "main" java.lang.Error
        at Test3.main(Test3.java:3)
```

---

### Case 3: Catching Exceptions Not Thrown

If there is no chance of a **checked exception** being raised within the `try` block, catching it results in a compilation error.

#### Example 1

```java
class Test3 {
    public static void main(String[] args) {
        try {
            System.out.println("Hello");
        } catch (IOException e) {} // Compile-time error
    }
}
```

**Error:**

```
Test3.java:5: exception java.io.IOException is never thrown in body of corresponding try statement
```

#### Example 2

```java
class Test3 {
    public static void main(String[] args) {
        try {
            int a = 10 / 2;
        } catch (FileNotFoundException e) {} // Compile-time error
    }
}
```

**Error:**

```
Test3.java:5: exception java.io.FileNotFoundException is never thrown in body of corresponding try statement
```

> This rule applies only to fully checked exceptions.

---

### Case 4: `throws` Not Applicable to Classes

The `throws` keyword can be used only with **methods** and **constructors**, not with **classes**.

---

## Summary of Exception Handling Keywords

1. **try** – To enclose risky code.
2. **catch** – To define exception handling code.
3. **finally** – To define cleanup code that runs irrespective of exception.
4. **throw** – To explicitly throw an exception.
5. **throws** – To delegate exception handling responsibility to the caller.

---

## Common Compile-Time Errors

1. Exception `XXX` has already been caught.
2. Unreported exception `XXX`; must be caught or declared to be thrown.
3. Exception `XXX` is never thrown in body of corresponding try statement.
4. Try without catch or finally.
5. Catch without try.
6. Finally without try.
7. Incompatible types:

   * Found: `Test`
   * Required: `java.lang.Throwable`
