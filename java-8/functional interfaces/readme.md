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

Java SE 8 included four main kinds of functional interfaces which can be applied in multiple situations as mentioned below:

- Consumer
- Predicate
- Function 
- Supplier

### Predicate
- A predicate is a function with a single argument and returns a boolean value.
- To implement predicate functions in Java, Oracle introduced the `Predicate` interface in Java 8 (i.e., `Predicate<T>`).
- The `Predicate` interface is present in the `java.util.function` package.
- It is a functional interface and contains only one method: `test()`.

#### Example:
```java
interface Predicate<T> {
    boolean test(T t);
}
```

As `Predicate` is a functional interface, it can refer to a lambda expression.

#### Example 1: Write a predicate to check whether the given integer is greater than 10 or not.
```java
public boolean test(Integer I) {
    return I > 10;
}
```

Using lambda expression:
```java
Predicate<Integer> p = I -> I > 10;
System.out.println(p.test(100)); // true
System.out.println(p.test(7));   // false
```

#### Full Program:
```java
import java.util.function.Predicate;

public class Test {
    public static void main(String[] args) {
        Predicate<Integer> p = i -> i > 10;
        System.out.println(p.test(100)); // true
        System.out.println(p.test(7));   // false
    }
}
```

### More Examples:
#### 1. Write a predicate to check if the length of a given string is greater than 3.
```java
Predicate<String> p = s -> s.length() > 3;
System.out.println(p.test("rvkb")); // true
System.out.println(p.test("rk"));  // false
```

#### 2. Write a predicate to check whether the given collection is empty or not.
```java
Predicate<Collection> p = c -> c.isEmpty();
```

### Predicate Joining
It is possible to join predicates into a single predicate using the following methods:
- `and()`
- `or()`
- `negate()`

These methods are equivalent to logical AND (`&&`), OR (`||`), and NOT (`!`) operators, respectively.

#### Example:
```java
import java.util.function.Predicate;

public class PredicateExample {
    public static void main(String[] args) {
        int[] x = {0, 5, 10, 15, 20, 25, 30};
        Predicate<Integer> p1 = i -> i > 10;
        Predicate<Integer> p2 = i -> i % 2 == 0;
        
        System.out.println("Numbers Greater Than 10:");
        filterNumbers(p1, x);
        
        System.out.println("Even Numbers:");
        filterNumbers(p2, x);
        
        System.out.println("Numbers Not Greater Than 10:");
        filterNumbers(p1.negate(), x);
        
        System.out.println("Numbers Greater Than 10 AND Even:");
        filterNumbers(p1.and(p2), x);
        
        System.out.println("Numbers Greater Than 10 OR Even:");
        filterNumbers(p1.or(p2), x);
    }
    
    public static void filterNumbers(Predicate<Integer> p, int[] x) {
        for (int num : x) {
            if (p.test(num)) {
                System.out.println(num);
            }
        }
    }
}
```
This program demonstrates different predicate operations, including negation, conjunction (`and`), and disjunction (`or`).

### Function
- Functions are similar to predicates except that functions can return any type of result.
- A function should return only one value, but that value can be of any type as per the requirement.
- To implement functions, Oracle introduced the `Function` interface in Java 8.
- The `Function` interface is present in the `java.util.function` package.
- It contains only one method: `apply()`.

#### Example:
```java
interface Function<T, R> {
    R apply(T t);
}
```

#### Example: Write a function to find the length of a given input string.
```java
import java.util.function.Function;

public class FunctionExample {
    public static void main(String[] args) {
        Function<String, Integer> f = s -> s.length();
        System.out.println(f.apply("Durga")); // 5
        System.out.println(f.apply("Soft"));  // 4
    }
}
```

### Consumer
- A `Consumer` is a function that takes a single argument and does not return any value.
- The `Consumer` interface is present in the `java.util.function` package.
- It contains only one method: `accept()`.

#### Example:
```java
import java.util.function.Consumer;

public class ConsumerExample {
    public static void main(String[] args) {
        Consumer<String> c = s -> System.out.println(s);
        c.accept("Hello World");
    }
}
```

### Supplier
- A `Supplier` is a function that takes no arguments and returns a value.
- The `Supplier` interface is present in the `java.util.function` package.
- It contains only one method: `get()`.

#### Example:
```java
import java.util.function.Supplier;

public class SupplierExample {
    public static void main(String[] args) {
        Supplier<Double> s = () -> Math.random();
        System.out.println(s.get());
        System.out.println(s.get());
    }
}
```



