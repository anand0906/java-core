# Lambda Expressions in Java

## Introduction

Lambda expressions in Java are a concise way to express instances of single-method interfaces (functional interfaces). They provide a way to write anonymous methods in a functional style, reducing boilerplate code and improving readability.

A lambda expression is an **anonymous (nameless) function** that does not have a name, return type, or access modifiers.

---

## Syntax of Lambda Expressions

### Traditional Method

```java
public void m1() {
    System.out.println("Hello");
}
```

### Using Lambda Expression

```java
() -> {
    System.out.println("Hello");
};
```

### Simplified Further

```java
() -> System.out.println("Hello");
```

---

## Examples

### Example 1: Adding Two Numbers

#### Traditional Method

```java
public void add(int a, int b) {
    System.out.println(a + b);
}
```

#### Using Lambda Expression

```java
(a, b) -> System.out.println(a + b);
```

---

### Example 2: Returning a String

#### Traditional Method

```java
public String str(String str) {
    return str;
}
```

#### Using Lambda Expression

```java
(String str) -> return str;
```

#### Simplified Further

```java
(str) -> str;
```

---

## Key Points about Lambda Expressions

1. **A lambda expression can have zero or more parameters.**

   ```java
   () -> System.out.println("Hello");
   (int a) -> System.out.println(a);
   (int a, int b) -> return a + b;
   ```

2. **Type inference** (The type of the parameter can be decided by compiler automatically) is supported. If the compiler can infer the type, it can be omitted.

   ```java
   (int a, int b) -> System.out.println(a + b);
   (a, b) -> System.out.println(a + b);
   ```

3. **Multiple parameters must be separated with commas.**

   ```java
   (a, b) -> System.out.println(a + b);
   ```

4. **If there are no parameters, an empty ****************************************`()`**************************************** should be used.**

   ```java
   () -> System.out.println("Hello");
   ```

5. **If there is a single parameter and the type can be inferred, parentheses can be omitted.**

   ```java
   (int a) -> System.out.println(a);
   (a) -> System.out.println(a);
   a -> System.out.println(a);
   ```

6. **Lambda bodies can contain multiple statements, enclosed in curly braces ****************************************`{}`****************************************.**

   ```java
   (a, b) -> {
       int sum = a + b;
       System.out.println("Sum: " + sum);
   };
   ```

7. **Lambda expressions require functional interfaces.**
   A functional interface is an interface that contains exactly one abstract method.

   ```java
   @FunctionalInterface
   interface MyFunctionalInterface {
       void display();
   }

   public class LambdaDemo {
       public static void main(String[] args) {
           MyFunctionalInterface obj = () -> System.out.println("Hello, Lambda!");
           obj.display();
       }
   }
   ```

---

## Benefits of Lambda Expressions

- **Concise code**: Reduces the amount of boilerplate code.
- **Improved readability**: Code is easier to understand.
- **Encourages functional programming**: Makes use of higher-order functions.
- **Better performance**: Eliminates the need for anonymous inner classes.

---
