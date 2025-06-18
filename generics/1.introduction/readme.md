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

Generics improve code clarity, type safety, and runtime reliability. They're a key part of Java’s type system since version 1.5.
