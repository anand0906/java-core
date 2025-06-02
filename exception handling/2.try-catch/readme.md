**Customized Exception Handling using try-catch in Java**

* It is **highly recommended** to handle exceptions.
* In a program, the code which may raise an exception is called **risky code**. Risky code should be placed inside a `try` block and the handling logic inside a `catch` block.

**Syntax:**

```java
try {
    // Risky code
} catch (Exception e) {
    // Handling code
}
```

---

### Without try-catch vs With try-catch

**Without try-catch:**

```java
class Test {
    public static void main(String[] args) {
        System.out.println("statement1");
        System.out.println(10/0);
        System.out.println("statement3");
    }
}
```

**Output:**

```
statement1
Exception in thread "main" java.lang.ArithmeticException: / by zero
    at Test.main()
```

**Result: Abnormal Termination**

**With try-catch:**

```java
class Test {
    public static void main(String[] args) {
        System.out.println("statement1");
        try {
            System.out.println(10/0);
        } catch (ArithmeticException e) {
            System.out.println(10/2);
        }
        System.out.println("statement3");
    }
}
```

**Output:**

```
statement1
5
statement3
```

**Result: Normal Termination**

---

### Execution Flow in try-catch

```java
try {
    statement1;
    statement2;
    statement3;
} catch(X e) {
    statement4;
}
statement5;
```

* **Case 1:** No exception → `1, 2, 3, 5` → Normal termination
* **Case 2:** Exception at `statement2`, matched catch → `1, 4, 5` → Normal termination
* **Case 3:** Exception at `statement2`, unmatched catch → `1` → Abnormal termination
* **Case 4:** Exception at `statement4` or `statement5` → Abnormal termination

**Notes:**

1. If an exception occurs in try block, remaining try statements are skipped.
2. Any exception raised outside of try block causes abnormal termination.
3. Exceptions may occur in `catch` and `finally` blocks as well.

---

### Methods to Print Exception Info

`Throwable` class provides methods:

* **printStackTrace()**: Prints the name, description, and stack trace of the exception.
* **toString()**: Prints exception name and description.
* **getMessage()**: Prints only the description of the exception.

**Note:** Default exception handler uses `printStackTrace()` internally.

---

### try with Multiple catch Blocks

* Different exceptions should be handled in separate catch blocks.
* This approach is **recommended** for better exception handling.

**Example:**

```java
try {
    // Risky code
} catch (FileNotFoundException e) {
    // use local file
} catch (ArithmeticException e) {
    // perform alternative arithmetic operation
} catch (SQLException e) {
    // switch to alternate DB
} catch (Exception e) {
    // default handler
}
```

**Incorrect Approach:**

```java
try {
    // Risky code
} catch (Exception e) {
    // handles all types of exceptions — not ideal
}
```

---

### Order of catch blocks

* Must be from **child to parent**.
* If placed from parent to child → **Compile Time Error**: `exception xxx has already been caught`

**Incorrect Order (Leads to Compilation Error):**

```java
try {
    System.out.println(10/0);
} catch (Exception e) {
    e.printStackTrace();
} catch (ArithmeticException e) {
    e.printStackTrace();
}
```

**Correct Order:**

```java
try {
    System.out.println(10/0);
} catch (ArithmeticException e) {
    e.printStackTrace();
} catch (Exception e) {
    e.printStackTrace();
}
```

**Output:** Compiles and runs successfully
