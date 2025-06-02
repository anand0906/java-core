# Top-10 Exceptions in Java

In Java, based on who raises the exception, all exceptions are divided into two types:

## 1. JVM Exceptions

These exceptions are raised **automatically by the JVM** whenever a specific event occurs.

### Examples:

* `ArrayIndexOutOfBoundsException`
* `NullPointerException`

## 2. Programmatic Exceptions

These exceptions are raised **explicitly by the programmer** or by an **API developer**.

### Example:

* `IllegalArgumentException`

---

## Top 10 Exceptions:

### 1. ArrayIndexOutOfBoundsException

Child class of `RuntimeException`, hence unchecked.
Raised automatically when trying to access an array element with an out-of-range index.

```java
class Test {
    public static void main(String[] args) {
        int[] x = new int[10];
        System.out.println(x[0]);       // valid
        System.out.println(x[100]);     // AIOOBE
        System.out.println(x[-100]);    // AIOOBE
    }
}
```

### 2. NullPointerException

Child class of `RuntimeException`, hence unchecked.
Raised when calling any method on a null reference.

```java
class Test {
    public static void main(String[] args) {
        String s = null;
        System.out.println(s.length()); // NPE
    }
}
```

### 3. StackOverflowError

Child class of `Error`, hence unchecked.
Occurs when there is infinite recursive method calling.

```java
class Test {
    public static void methodOne() {
        methodTwo();
    }
    public static void methodTwo() {
        methodOne();
    }
    public static void main(String[] args) {
        methodOne();
    }
}
```

### 4. NoClassDefFoundError

Child class of `Error`, hence unchecked.
Occurs when JVM cannot find the required `.class` file.

* Example: `java Test` when `Test.class` is not available.

### 5. ClassCastException

Child class of `RuntimeException`, hence unchecked.
Occurs when trying to typecast a parent object to a child type.

```java
class Animal {}
class Dog extends Animal {
    void bark() {}
}
class Test {
    public static void main(String[] args) {
        Animal a = new Animal();
        Dog d = (Dog) a; // ClassCastException
        d.bark();
    }
}
```

### 6. ExceptionInInitializerError

Child class of `Error`, hence unchecked.
Occurs if an exception happens during static variable initialization or static block execution.

```java
class Test {
    static int i = 10 / 0; // ArithmeticException
}
```

Output:

```
Exception in thread "main" java.lang.ExceptionInInitializerError
```

```java
class Test {
    static {
        String s = null;
        System.out.println(s.length());
    }
}
```

Output:

```
Exception in thread "main" java.lang.ExceptionInInitializerError
```

### 7. IllegalArgumentException

Child class of `RuntimeException`, hence unchecked.
Raised explicitly when a method is invoked with an inappropriate argument.

```java
class Test {
    public static void main(String[] args) {
        Thread t = new Thread();
        t.setPriority(10);   // valid
        t.setPriority(100);  // invalid, IAE
    }
}
```

Output:

```
Exception in thread "main" java.lang.IllegalArgumentException
```

### 8. NumberFormatException

Child class of `IllegalArgumentException`, hence unchecked.
Raised when converting a string to a number, but the string is not properly formatted.

```java
class Test {
    public static void main(String[] args) {
        int i = Integer.parseInt("10");  // valid
        int j = Integer.parseInt("ten"); // NFE
    }
}
```

Output:

```
Exception in thread "main" java.lang.NumberFormatException: For input string: "ten"
```

### 9. IllegalStateException

Child class of `RuntimeException`, hence unchecked.
Occurs when a method is called at an inappropriate time.

```java
HttpSession session = req.getSession();
System.out.println(session.getId());
session.invalidate();
System.out.println(session.getId()); // IllegalStateException
```

### 10. AssertionError

Child class of `Error`, hence unchecked.
Raised explicitly when an assertion fails.

```java
class Test {
    public static void main(String[] args) {
        assert (false); // AssertionError
    }
}
```

---

## Summary Table: Raised By Whom?

| Exception/Error                | Raised By                  |
| ------------------------------ | -------------------------- |
| ArrayIndexOutOfBoundsException | JVM                        |
| NullPointerException           | JVM                        |
| StackOverflowError             | JVM                        |
| NoClassDefFoundError           | JVM                        |
| ClassCastException             | JVM                        |
| ExceptionInInitializerError    | JVM                        |
| IllegalArgumentException       | Programmer / API Developer |
| NumberFormatException          | Programmer / API Developer |
| IllegalStateException          | Programmer / API Developer |
| AssertionError                 | Programmer / API Developer |
