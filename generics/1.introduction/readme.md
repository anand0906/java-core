# Introduction to Java Generics

## Definition:

The main objective of Generics is to:

* Provide **type safety**
* Resolve **type-casting** problems

---

## Case 1: Type Safety

### Arrays Are Type Safe

* Arrays are always type safe. We can guarantee the type of elements stored inside an array.
* If we need to hold only `String` type objects, we can use a `String` array.
* If we try to add any other type of object, the compiler will throw a compile-time error.

### Example:

```java
class Test {
  public static void main(String[] args) {
    String[] strArray = new String[3];
    strArray[0] = "apple";
    strArray[1] = "banana";
    strArray[2] = 10; // Compile-time error: incompatible types
  }
}
```

* This ensures type safety and prevents runtime errors.

### Collections Are Not Type Safe (Before Generics)

* Collections like `ArrayList` are not type safe without generics.
* If our requirement is to store only `String` objects, using raw `ArrayList` is not recommended.
* Adding other types won't show a compile-time error, but can lead to runtime failures.

### Example:

```java
import java.util.*;

class Test {
  public static void main(String[] args) {
    ArrayList list = new ArrayList();
    list.add("apple");
    list.add(10); // No compile-time error

    String s = (String) list.get(1); // Runtime error: ClassCastException
    System.out.println(s);
  }
}
```

* This can result in `ClassCastException` at runtime.

---

## Case 2: Type-Casting

### Arrays Don’t Require Type-Casting at Retrieval

```java
class Test {
  public static void main(String[] args) {
    String[] strArray = {"a", "b", "c"};
    String s = strArray[0]; // No casting needed
    System.out.println(s);
  }
}
```

### Collections Require Type-Casting at Retrieval (Before Generics)

```java
import java.util.*;

class Test {
  public static void main(String[] args) {
    ArrayList list = new ArrayList();
    list.add("hello");

    String s = (String) list.get(0); // Explicit casting required
    System.out.println(s);
  }
}
```

* Type casting becomes a frequent and error-prone task.

---

## Solution: Generics

To overcome the problems of:

* Lack of type safety
* Type casting requirements

Generics were introduced in Java 1.5.

### Objectives of Generics:

1. To provide type safety to collections.
2. To eliminate the need for explicit type casting.

### Example:

```java
import java.util.*;

class Test {
  public static void main(String[] args) {
    ArrayList<String> list = new ArrayList<String>();
    list.add("apple");
    list.add("banana");
    // list.add(100); // Compile-time error

    String s = list.get(0); // No casting required
    System.out.println(s);
  }
}
```

* We can add only `String` type objects.
* If we try to add another type, it results in a compile-time error.
* Retrieval does not require explicit casting.

---

## Conclusion 1:

Polymorphism applies only to the **base type**, not to the **parameter type**.

### Example:

```java
ArrayList<String> l1 = new ArrayList<String>();
ArrayList<Object> l2 = new ArrayList<String>(); // Compile-time error
```

* You cannot use a parent-type reference (`Object`) to refer to a child-type generic (`String`).

---

## Conclusion 2:

Collections work only with **objects**.

* We cannot use **primitive types** like `int`, `float`, `char` directly.
* We must use wrapper classes like `Integer`, `Float`, `Character`.

### Example:

```java
ArrayList<int> list = new ArrayList<int>(); // Compile-time error
ArrayList<Integer> list = new ArrayList<Integer>(); // Correct
```

* Always use wrapper classes for primitives in generics.

---

# Java Generics

## Overview

Generic classes provide type safety and eliminate the need for type casting in Java. They were introduced in Java 1.5 to address the limitations of non-generic collections.

## Non-Generic Classes (Before Java 1.5)

### ArrayList Before Generics

Until Java 1.4, the ArrayList class was declared without generics:

```java
class ArrayList {
    add(Object o);
    Object get(int index);
}
```

### Problems with Non-Generic Approach

- **No Type Safety**: The `add()` method accepts `Object` as argument, allowing any type of object to be added
- **Type Casting Required**: The `get()` method returns `Object`, requiring explicit type casting during retrieval
- **Runtime Errors**: Type mismatches are only caught at runtime

---

## Generic Classes (Java 1.5+)

### Generic ArrayList Declaration

In Java 1.5, ArrayList was redesigned with generics:

```java
class ArrayList<T> {
    add(T t);
    T get(int index);
}
```

### Benefits of Generic Approach

- **Type Safety**: Only specified type can be added to the collection
- **No Type Casting**: Direct assignment without casting
- **Compile-Time Error Detection**: Type mismatches caught during compilation

### Example Usage

```java
ArrayList<String> l = new ArrayList<String>();
```

The compiler treats this as:

```java
class ArrayList {
    add(String s);
    String get(int index);
}
```

#### Type Safety Example

```java
// This will cause compile-time error if trying to add non-String objects
l.add("Hello"); // Valid
l.add(123);     // Compile-time error
```

#### No Type Casting Required

```java
// Direct assignment without casting
String value = l.get(0); // No need for (String) l.get(0)
```

## Creating Custom Generic Classes

### Generic Class Definition

A generic class is a class with type parameters. You can create your own generic classes:

```java
class Account<T> {
    // Class implementation
}

// Usage
Account<String> g1 = new Account<String>();
Account<Integer> g2 = new Account<Integer>();
```

### Complete Generic Class Example

```java
class Gen<T> {
    T obj;
    
    Gen(T obj) {
        this.obj = obj;
    }
    
    public void show() {
        System.out.println("The type of object is: " + obj.getClass().getName());
    }
    
    public T getObject() {
        return obj;
    }
}
```

### Demo Implementation

```java
class GenericsDemo {
    public static void main(String[] args) {
        // Integer generic
        Gen<Integer> g1 = new Gen<Integer>(10);
        g1.show();
        System.out.println(g1.getObject());
        
        // String generic
        Gen<String> g2 = new Gen<String>("Akshay");
        g2.show();
        System.out.println(g2.getObject());
        
        // Double generic
        Gen<Double> g3 = new Gen<Double>(10.5);
        g3.show();
        System.out.println(g3.getObject());
    }
}
```

### Output

```
The type of object is: java.lang.Integer
10
The type of object is: java.lang.String
Akshay
The type of object is: java.lang.Double
10.5
```

---
