### What is an Exception?

* An **exception** is an unwanted or unexpected event that disturbs the normal flow of the program.
* **Examples**:

  * `SleepingException`
  * `TyrePunchuredException`
  * `FileNotFoundException`

### Why Handle Exceptions?

* It is highly recommended to handle exceptions to ensure **graceful (normal) termination** of the program.

### What is Exception Handling?

* Exception handling **does not mean fixing** the error.
* It means **providing an alternative path** to allow the program to continue running normally.

**Example Scenario**:

* Suppose we need to read data from a file located in London.
* If the file is not available, the program should not crash.
* Instead, we can read from a local file.

```java
try {
    // read data from London file
} catch (FileNotFoundException e) {
    // use local file and continue the program
}
```

### JVM and Method Call Stack

* Every thread gets a **separate stack** when created.
* All method calls are stored as **stack frames** or **activation records**.
* After a method finishes, its frame is removed from the stack.
* When all methods complete, JVM destroys the stack and terminates the program.

**Example:**

```java
class Test {
    public static void main(String[] args){
        doStuff();
    }

    public static void doStuff(){
        doMoreStuff();
    }

    public static void doMoreStuff(){
        System.out.println("Hello");
    }
}
```

**Output:**

```
Hello
```

### Default Exception Handling in Java

**How it works:**

1. If an exception is raised, the method:

   * Creates an exception object with:

     * Name
     * Description
     * Stack trace (location)
2. The method hands the exception to **JVM**.
3. JVM checks if the method has a `try-catch` block.

   * If not, it **terminates that method abnormally**.
4. JVM checks the **caller method** for handling code.

   * If not, it also terminates abnormally.
5. This continues up to the `main()` method.
6. If `main()` doesn't handle it:

   * JVM hands it to the **Default Exception Handler**.
   * The handler prints the exception and **terminates the program**.

**Output Format:**

```
Exception in thread "main" java.lang.ExceptionName: description
    at ClassName.methodName(FileName.java:lineNumber)
    ...
```

**Example:**

```java
class Test {
    public static void main(String[] args){
        doStuff();
    }

    public static void doStuff(){
        doMoreStuff();
    }

    public static void doMoreStuff(){
        System.out.println(10/0);
    }
}
```

**Output:**

```
Exception in thread "main" java.lang.ArithmeticException: / by zero
    at Test.doMoreStuff(Test.java:10)
    at Test.doStuff(Test.java:7)
    at Test.main(Test.java:4)
```
**Exception Hierarchy in Java**

`Throwable` acts as the root for the exception hierarchy in Java. It has two direct child classes:

1. **Exception**

   * Generally caused by our program and are **recoverable**.
   * **Example**: If `FileNotFoundException` occurs, we can use a local file and continue the execution of the program.

2. **Error**

   * Generally **not caused by our program**, but due to **lack of system resources**. These are **non-recoverable**.
   * **Example**: If `OutOfMemoryError` occurs, the program terminates abnormally. The **System Admin** or **Server Admin** is responsible for resolving it by increasing heap memory.

---

**Checked vs Unchecked Exceptions**

* **Checked Exceptions**:
  The exceptions which are checked by the compiler whether programmer handling or not, for smooth execution of the program at runtime, are called checked exceptions.

  * **Examples**:

    1. `HallTicketMissingException`
    2. `PenNotWorkingException`
    3. `FileNotFoundException`

* **Unchecked Exceptions**:
  The exceptions which are not checked by the compiler whether programmer handing or not ,are called unchecked exceptions.

  * **Examples**:

    1. `BombBlastException`
    2. `ArithmeticException`
    3. `NullPointerException`

**Note:**

* `RuntimeException` and its child classes, `Error` and its child classes are **unchecked**.
* All other exceptions are considered **checked**.
* Regardless of being checked or unchecked, exceptions **always occur at runtime**. There is **no exception** that occurs during **compile time**.

---

**Fully Checked vs Partially Checked Exceptions**

* **Fully Checked Exception**: A checked exception is said to be fully checked if and only if all its child classes are also checked.

  * **Examples**:

    1. `IOException`
    2. `InterruptedException`

* **Partially Checked Exception**: A checked exception is said to be partially checked if and only if some of its child classes are unchecked.

  * **Examples**:

    1. `Exception`
    2. `Throwable`

---

**Summary Table:**

| Exception Type          | Category          |
| ----------------------- | ----------------- |
| `RuntimeException`      | Unchecked         |
| `Error`                 | Unchecked         |
| `IOException`           | Fully Checked     |
| `Exception`             | Partially Checked |
| `InterruptedException`  | Fully Checked     |
| `Throwable`             | Partially Checked |
| `ArithmeticException`   | Unchecked         |
| `NullPointerException`  | Unchecked         |
| `FileNotFoundException` | Fully Checked     |
