# Functional Interfaces in Java

## What is a Functional Interface?

A **functional interface** is an interface that contains only **one abstract method**. This method is also known as the **functional method** or **Single Abstract Method (SAM)**.

### Examples of Functional Interfaces:

1. **Runnable** -> Contains only `run()` method
2. **Comparable** -> Contains only `compareTo()` method
3. **ActionListener** -> Contains only `actionPerformed()` method
4. **Callable** -> Contains only `call()` method

In addition to the single abstract method, a functional interface can contain **default and static methods**.

```java
interface Interf {
    void m1();
    default void m2() {
        System.out.println("Hello");  
    }
}
```

### `@FunctionalInterface` Annotation

In Java 8, **`@FunctionalInterface`** annotation was introduced to indicate that an interface is a **functional interface**.

```java
@FunctionalInterface
interface Interf {
    void m1();  // Valid Functional Interface
}
```

If we declare more than **one abstract method**, the compiler will throw an error.

```java
@FunctionalInterface
interface Interf {
    void m1();
    void m2();  // Compilation Error: Functional interfaces can have only one abstract method
}
```

If an interface does not contain an abstract method, it is **not** a functional interface:

```java
@FunctionalInterface
interface Interf {  // Compilation Error: Must have exactly one abstract method
}
```

---

## Functional Interface with Inheritance

### **Valid Functional Interface with Inheritance**

If a child interface does not declare any **new** abstract methods, it is still a functional interface.

```java
@FunctionalInterface
interface A {
    void methodOne();
}
@FunctionalInterface
interface B extends A {
    // No additional abstract methods, still a Functional Interface
}
```

If the child interface declares the **same** abstract method, it remains a **functional interface**:

```java
@FunctionalInterface
interface A {
    void methodOne();
}
@FunctionalInterface
interface B extends A {
    void methodOne();  // No new abstract methods
}
```

### **Invalid Functional Interface with Inheritance**

If the child interface declares a **new** abstract method, it is **no longer** a functional interface.

```java
@FunctionalInterface
interface A {
    void methodOne();
}
@FunctionalInterface  // Compilation Error
interface B extends A {
    void methodTwo();  // Additional abstract method - Invalid
}
```

However, if `@FunctionalInterface` is **not** used, it is a valid interface:

```java
interface A {
    void methodOne();
}
interface B extends A {
    void methodTwo();  // Normal Interface - No error
}
```

### Default Methods in Functional Interfaces

**Default methods** can be added without restrictions:

```java
@FunctionalInterface
interface A {
    void methodOne();
    default void methodTwo() {
        System.out.println("Default method in Functional Interface");
    }
}
```

---

## Functional Interface vs Lambda Expressions

A **functional interface** is required to invoke a **lambda expression**. Functional interface references can be used to refer to lambda expressions.

### **Example: Without Lambda Expression**

```java
interface Interf {
    void methodOne();
}

class Demo implements Interf {
    public void methodOne() {
        System.out.println("Method One Execution");
    }
}

public class Test {
    public static void main(String[] args) {
        Interf i = new Demo();
        i.methodOne();
    }
}
```

### **Example: With Lambda Expression**

```java
interface Interf {
    void methodOne();
}

public class Test {
    public static void main(String[] args) {
        Interf i = () -> System.out.println("Method One Execution");
        i.methodOne();
    }
}
```

---

## More Examples with Lambda Expressions

### **Example: Without Lambda Expression (Addition)**

```java
interface Interf {
    void sum(int a, int b);
}

class Demo implements Interf {
    public void sum(int a, int b) {
        System.out.println("The Sum: " + (a + b));
    }
}

public class Test {
    public static void main(String[] args) {
        Interf i = new Demo();
        i.sum(20, 5);
    }
}
```

### **Example: With Lambda Expression (Addition)**

```java
interface Interf {
    void sum(int a, int b);
}

public class Test {
    public static void main(String[] args) {
        Interf i = (a, b) -> System.out.println("The Sum: " + (a + b));
        i.sum(5, 10);
    }
}
```

### **Example: Without Lambda Expression (Square Calculation)**

```java
interface Interf {
    int square(int x);
}

class Demo implements Interf {
    public int square(int x) {
        return x * x;
    }
}

public class Test {
    public static void main(String[] args) {
        Interf i = new Demo();
        System.out.println("The Square of 7 is: " + i.square(7));
    }
}
```

### **Example: With Lambda Expression (Square Calculation)**

```java
interface Interf {
    int square(int x);
}

public class Test {
    public static void main(String[] args) {
        Interf i = x -> x * x;
        System.out.println("The Square of 5 is: " + i.square(5));
    }
}
```

---

## Conclusion

- Functional interfaces enable **lambda expressions**.
- They must have **exactly one** abstract method.
- Java provides built-in functional interfaces like `Runnable`, `Callable`, and `Comparator`.
- The `@FunctionalInterface` annotation ensures an interface adheres to the functional interface contract.



