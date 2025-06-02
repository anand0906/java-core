### Finally Block in Java

* It is **not recommended** to take cleanup code inside the `try` block because there is **no guarantee** that every statement in the try block will be executed.
* It is **not recommended** to place cleanup code inside the `catch` block because if **no exception occurs**, the catch block won't be executed.
* We require a block that **always executes**, whether an exception is raised or not, and whether it is handled or not. This is the **`finally` block**.
* The main objective of the `finally` block is to **maintain cleanup code**.

#### Syntax:

```java
try {
    // risky code
} catch (Exception e) {
    // handling code
} finally {
    // cleanup code
}
```

### Behavior of `finally` Block

#### Case 1: No Exception

```java
class Test {
    public static void main(String[] args) {
        try {
            System.out.println("try block executed");
        } catch (ArithmeticException e) {
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

#### Case 2: Exception Raised and Catch Block Matches

```java
class Test {
    public static void main(String[] args) {
        try {
            System.out.println("try block executed");
            System.out.println(10 / 0);
        } catch (ArithmeticException e) {
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

#### Case 3: Exception Raised and Catch Block Doesn't Match

```java
class Test {
    public static void main(String[] args) {
        try {
            System.out.println("try block executed");
            System.out.println(10 / 0);
        } catch (NullPointerException e) {
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
Exception in thread "main" java.lang.ArithmeticException: / by zero
```

### `return` vs `finally`

Even if `return` statements are used in `try` or `catch`, the `finally` block is executed **before** the method returns.

#### Example:

```java
class Test {
    public static void main(String[] args) {
        try {
            System.out.println("try block executed");
            return;
        } catch (ArithmeticException e) {
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

#### If `return` is present in all blocks, `finally`'s return is considered:

```java
class Test {
    public static void main(String[] args) {
        System.out.println(m1());
    }
    public static int m1() {
        try {
            System.out.println(10 / 0);
            return 777;
        } catch (ArithmeticException e) {
            return 888;
        } finally {
            return 999;
        }
    }
}
```

**Output:**

```
999
```

### `finally` vs `System.exit(0)`

The only case where `finally` block won't execute is if `System.exit(0)` is called:

```java
class Test {
    public static void main(String[] args) {
        try {
            System.out.println("try");
            System.exit(0);
        } catch (ArithmeticException e) {
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

> JVM shuts down before `finally` executes.

### Differences: `final` vs `finally` vs `finalize`

| Keyword      | Description                                                                                          |
| ------------ | ---------------------------------------------------------------------------------------------------- |
| `final`      | Modifier for classes, methods, variables. Prevents inheritance, method overriding, and reassignment. |
| `finally`    | A block for cleanup code that always executes after try-catch.                                       |
| `finalize()` | A method invoked by Garbage Collector before destroying an object.                                   |

**Note:** Use `finally` for cleanup over `finalize()` due to unpredictable GC behavior.

### Control Flow in try-catch-finally

```java
try {
    stmt1;
    stmt2;
    stmt3;
} catch (Exception e) {
    stmt4;
} finally {
    stmt5;
}
stmt6;
```

* Case 1: No Exception → 1, 2, 3, 5, 6
* Case 2: Exception at 2, catch matches → 1, 4, 5, 6
* Case 3: Exception at 2, catch doesn't match → 1, 5 (abnormal termination)
* Case 4: Exception at 4 → 1, 2, 3, 5 (abnormal termination)
* Case 5: Exception at 5 or 6 → abnormal termination

### Nested try-catch-finally

```java
try {
    stmt1;
    stmt2;
    stmt3;
    try {
        stmt4;
        stmt5;
        stmt6;
    } catch (X e) {
        stmt7;
    } finally {
        stmt8;
    }
    stmt9;
} catch (Y e) {
    stmt10;
} finally {
    stmt11;
}
stmt12;
```

**Many cases exist** depending on where the exception is thrown and matched. Finally blocks always execute if the corresponding try is entered.

### Important Notes:

1. If try block is not entered, finally won’t execute.
2. Once inside try, JVM ensures finally is executed before exit.
3. Nested try-catch blocks are allowed.
4. Inner blocks handle specific exceptions; outer blocks handle general ones.

#### Example:

```java
class Test {
    public static void main(String[] args) {
        try {
            System.out.println(10 / 0);
        } catch (ArithmeticException e) {
            System.out.println(10 / 0);
        } finally {
            String s = null;
            System.out.println(s.length());
        }
    }
}
```

**Output:**

```
Exception in thread "main" java.lang.NullPointerException
```

> The most recently raised exception is handled by the default handler.


**Control Flow in try-catch-finally Blocks**

**Example:**

```java
try {
    stmt-1;
    stmt-2;
    stmt-3;
} catch(Exception e) {
    stmt-4;
} finally {
    stmt-5;
}
stmt-6;
```

**Cases:**

1. **No Exception:** `1, 2, 3, 5, 6` (Normal termination)
2. **Exception at stmt-2 and catch matched:** `1, 4, 5, 6` (Normal termination)
3. **Exception at stmt-2 and catch not matched:** `1, 5` (Abnormal termination)
4. **Exception at stmt-4:** Always abnormal, but `stmt-5` (finally) executes before termination.
5. **Exception at stmt-5 or stmt-6:** Always abnormal termination.

---

**Control Flow in Nested try-catch-finally Blocks**

**Example:**

```java
try {
    stmt-1;
    stmt-2;
    stmt-3;
    try {
        stmt-4;
        stmt-5;
        stmt-6;
    } catch (X e) {
        stmt-7;
    } finally {
        stmt-8;
    }
    stmt-9;
} catch (Y e) {
    stmt-10;
} finally {
    stmt-11;
}
stmt-12;
```

**Cases:**

1. No exception: `1–12` (Normal termination)
2. Exception at stmt-2, outer catch matched: `1, 10, 11, 12`
3. Exception at stmt-2, outer catch not matched: `1, 11` (Abnormal)
4. Exception at stmt-5, inner catch matched: `1–4, 7, 8, 9, 11, 12`
5. Exception at stmt-5, inner catch not matched but outer catch matched: `1–4, 8, 10, 11, 12`
6. Exception at stmt-5, no catch matched: `1–4, 8, 11` (Abnormal)
7. Exception at stmt-7, catch matched: `1–6, 8, 10, 11, 12`
8. Exception at stmt-7, catch not matched: `1–6, 8, 11` (Abnormal)
9. Exception at stmt-8, catch matched: `1–7, 10, 11, 12`
10. Exception at stmt-8, catch not matched: `1–7, 11` (Abnormal)
11. Exception at stmt-9, outer catch matched: `1–8, 10, 11, 12`
12. Exception at stmt-9, outer catch not matched: `1–8, 11` (Abnormal)
13. Exception at stmt-10: Always abnormal, but `stmt-11` executes before termination.
14. Exception at stmt-11 or stmt-12: Always abnormal termination.

---

**Notes:**

* If control never enters the `try` block, `finally` is not executed.
* Once inside a `try` block, control cannot leave without executing the `finally` block.
* Nested try-catch blocks are allowed.
* Inner `try-catch` should handle specific exceptions; outer should handle generalized exceptions.

**Example:**

```java
class Test {
    public static void main(String[] args) {
        try {
            System.out.println(10/0);
        } catch(ArithmeticException e) {
            System.out.println(10/0);
        } finally {
            String s = null;
            System.out.println(s.length());
        }
    }
}
```

**Output:** NullPointerException

---

**Rules for try-catch-finally Combinations**

1. `try` must be followed by either `catch` or `finally` or both.
2. `catch` and `finally` cannot exist without a `try` block.
3. `try-catch-finally` blocks must appear in order.
4. Nesting of `try-catch-finally` is valid.
5. Curly braces `{}` are mandatory for all blocks.

**Valid Examples:**

```java
try {} catch (X e) {}
try {} catch (X e) {} finally {}
try {} finally {}
```

**Invalid Examples:**

```java
try {} catch (X e) {} catch (X e) {} // Error: same exception caught twice
catch (X e) {} // Error: no try block
finally {} // Error: no try block
try {} System.out.println("Hello"); catch {} // Error: incorrect order
```

---

**Important:**

* Default exception handler handles only the most recently raised exception.
* Use specific exceptions in inner `try` and generic in outer for better control.

