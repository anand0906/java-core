# Java Generic Methods and Wildcard Character (?)

## Overview

Generic methods and wildcard characters provide flexibility in working with generic types. Wildcards allow methods to accept different parameterized types while maintaining type safety.

## Wildcard Types

### 1. Specific Type Parameter

```java
methodOne(ArrayList<String> l)
```

- This method is applicable **only** for ArrayList of String type
- Within the method, you can add only String type objects and null

**Example:**
```java
public static void methodOne(ArrayList<String> l) {
    l.add("A");     // valid
    l.add(null);    // valid
    l.add(10);      // invalid - compile error
}
```

### 2. Unbounded Wildcard (?)

```java
methodOne(ArrayList<?> l)
```

- Can be used for ArrayList of **any type**
- Within the method, you **cannot add anything** except null
- Useful for **read-only operations**

**Example:**
```java
public static void methodOne(ArrayList<?> l) {
    l.add(null);    // valid
    l.add("A");     // invalid - compile error
    l.add(10);      // invalid - compile error
    
    // Reading is allowed
    Object obj = l.get(0);  // valid
    int size = l.size();    // valid
}
```

### 3. Upper Bounded Wildcard (? extends)

```java
methodOne(ArrayList<? extends X> l)
```

- If X is a **class**: Method accepts ArrayList of X type or its **child classes**
- If X is an **interface**: Method accepts ArrayList of X type or its **implementation classes**
- Within the method, you **cannot add anything** except null

**Example:**
```java
public static void methodOne(ArrayList<? extends Number> l) {
    l.add(null);        // valid
    l.add(10);          // invalid - compile error
    l.add(10.5);        // invalid - compile error
    
    // Reading is allowed
    Number num = l.get(0);  // valid
    
    // Can call Number methods on retrieved objects
    for (Number n : l) {
        System.out.println(n.doubleValue());
    }
}

// Usage
ArrayList<Integer> intList = new ArrayList<>();
ArrayList<Double> doubleList = new ArrayList<>();
methodOne(intList);     // valid - Integer extends Number
methodOne(doubleList);  // valid - Double extends Number
```

### 4. Lower Bounded Wildcard (? super)

```java
methodOne(ArrayList<? super X> l)
```

- If X is a **class**: Method accepts ArrayList of X type or its **super classes**
- If X is an **interface**: Method accepts ArrayList of X type or super classes of implementation class of X
- Within the method, you **can add X type objects and null**

**Example:**
```java
public static void methodOne(ArrayList<? super Integer> l) {
    l.add(10);      // valid - can add Integer
    l.add(null);    // valid
    l.add(10.5);    // invalid - Double is not Integer or its subtype
    
    // Reading returns Object
    Object obj = l.get(0);  // valid
}

// Usage
ArrayList<Integer> intList = new ArrayList<>();
ArrayList<Number> numList = new ArrayList<>();
ArrayList<Object> objList = new ArrayList<>();

methodOne(intList);  // valid - Integer super Integer
methodOne(numList);  // valid - Number super Integer  
methodOne(objList);  // valid - Object super Integer
```

## Wildcard Declaration Examples

### Valid Declarations

```java
// 1. Specific type
ArrayList<String> l1 = new ArrayList<String>();        // valid

// 2. Unbounded wildcard
ArrayList<?> l2 = new ArrayList<String>();             // valid

// 3. Upper bounded wildcard
ArrayList<? extends Number> l3 = new ArrayList<Integer>(); // valid

// 4. Lower bounded wildcard
ArrayList<? super Integer> l4 = new ArrayList<Number>();   // valid
```

### Invalid Declarations

```java
// 5. Cannot instantiate with wildcard
ArrayList<?> l5 = new ArrayList<?>();                  // invalid
// Compile Error: incompatible types
// Found: java.util.ArrayList<?>
// Required: java.util.ArrayList<?>

// 6. Cannot instantiate with bounded wildcard
ArrayList<? extends Number> l6 = new ArrayList<? extends Number>(); // invalid
// Compile Error: unexpected type
// found: ? extends java.lang.Number
// required: class or interface without bounds

// 7. Cannot instantiate with unbounded wildcard
ArrayList<?> l7 = new ArrayList<?>();                  // invalid
// Compile Error: unexpected type
// Found: ?
// Required: class or interface without bounds
```

## Generic Methods

### Method-Level Type Parameters

Type parameters can be declared at method level, just before the return type:

```java
public class GenericMethodExample {
    
    // Valid generic method declarations
    public <T> void methodOne1(T t) { }                    // valid
    
    public <T> void methodOne2(ArrayList<T> t) { }         // valid
    
    public <T extends Number> void methodOne3(T t) { }     // valid
    
    public <T extends Runnable> void methodOne4(T t) { }   // valid
    
    // Invalid - missing generic declaration
    public void methodOne(T t) { }                         // invalid
    // Compile Error: cannot find symbol T
    
    // Valid with bounds
    public <T extends Number & Runnable> void methodSix(T t) { } // valid
    
    // Invalid - implements keyword not allowed
    public <T implements Runnable> void methodSeven(T t) { }     // invalid
    // Compile Error: interface expected here
}
```

### Generic Method Usage Example

```java
public class GenericMethodDemo {
    
    public static <T> void printArray(T[] array) {
        for (T element : array) {
            System.out.println(element);
        }
    }
    
    public static <T extends Comparable<T>> T findMax(T[] array) {
        T max = array[0];
        for (T element : array) {
            if (element.compareTo(max) > 0) {
                max = element;
            }
        }
        return max;
    }
    
    public static void main(String[] args) {
        String[] stringArray = {"Hello", "World", "Java"};
        Integer[] intArray = {1, 5, 3, 9, 2};
        
        printArray(stringArray);  // T inferred as String
        printArray(intArray);     // T inferred as Integer
        
        String maxString = findMax(stringArray);
        Integer maxInt = findMax(intArray);
        
        System.out.println("Max string: " + maxString);
        System.out.println("Max integer: " + maxInt);
    }
}
```

## Communication with Non-Generic Code

### Compatibility with Legacy Code

Java maintains backward compatibility with pre-generic code, which can lead to some compromises:

```java
import java.util.*;

class Test {
    public static void main(String[] args) {
        ArrayList<String> l = new ArrayList<String>();
        l.add("A");
        // l.add(10);     // Compile Error: cannot find symbol method add(int)
        
        methodOne(l);     // Passing to non-generic method
        
        // After methodOne call, the list may contain non-String objects
        // l.add(10.5);   // Compile Error: cannot find symbol method add(double)
        
        System.out.println(l);  // [A, 10, 10.5, true]
    }
    
    // Non-generic method - accepts raw ArrayList
    public static void methodOne(ArrayList l) {
        l.add(10);      // Raw type allows any object
        l.add(10.5);
        l.add(true);
    }
}
```

## Type Erasure and Runtime Behavior

### Key Conclusions

1. **Compile-Time Only**: Generics concept applies only at compile time
2. **Runtime Equality**: At runtime, there is no generic type information
3. **Type Erasure**: All generic type information is erased during compilation

### Runtime Equivalence

```java
// All these declarations are equal at runtime
ArrayList l1 = new ArrayList();
ArrayList<String> l2 = new ArrayList<String>();
ArrayList<Integer> l3 = new ArrayList<Integer>();
```

### Example 1: Runtime Behavior

```java
import java.util.*;

class Test {
    public static void main(String[] args) {
        ArrayList l = new ArrayList();  // Raw type
        l.add(10);
        l.add(10.5);
        l.add(true);
        System.out.println(l);  // [10, 10.5, true]
    }
}
```

### Example 2: Method Overloading Conflict

```java
import java.util.*;

class Test {
    // This causes compile error due to type erasure
    public void methodOne(ArrayList<String> l) { }
    public void methodOne(ArrayList<Integer> l) { }
    
    // Compile Error: name clash
    // methodOne(java.util.ArrayList<String>) and 
    // methodOne(java.util.ArrayList<Integer>) have the same erasure
}
```

### Type Safety Example

```java
// Both declarations are equivalent and type-safe
ArrayList<String> l1 = new ArrayList<String>();
ArrayList<String> l2 = new ArrayList<>();  // Diamond operator (Java 7+)

// Both allow only String objects
l1.add("A");        // valid
l1.add(10);         // invalid - compile error

l2.add("Hello");    // valid  
l2.add(10);         // invalid - compile error
```

## Best Practices

1. **Use Wildcards for Flexibility**: Use `?` when you need to accept multiple parameterized types
2. **Upper Bounds for Reading**: Use `? extends T` when you only need to read from the collection
3. **Lower Bounds for Writing**: Use `? super T` when you need to add elements to the collection
4. **Generic Methods**: Use method-level generics for utility methods that work with multiple types
5. **Avoid Raw Types**: Always use parameterized types to maintain type safety

## Summary

- **Unbounded Wildcard (?)**: Read-only access to collections of any type
- **Upper Bounded (? extends)**: Read-only access to collections of specific type hierarchy
- **Lower Bounded (? super)**: Write access for specific type and its subtypes
- **Generic Methods**: Provide type safety at method level
- **Type Erasure**: Generic information exists only at compile time
