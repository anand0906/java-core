# Java Bounded Types

## Overview

Bounded types allow you to restrict the type parameter to a particular range using the `extends` keyword. This provides more control over what types can be used with your generic classes.

## Unbounded Types vs Bounded Types

### Unbounded Types

```java
class Test<T> {
    // T can be any type
}

Test<Integer> t1 = new Test<Integer>();
Test<String> t2 = new Test<String>();
```

In unbounded types, there are no restrictions on the type parameter - any type can be passed.

### Bounded Types

```java
class Test<T extends SomeClass> {
    // T must be SomeClass or its subclass
}
```

- If `x` is a **class**: Type parameter can be `x` or its child classes
- If `x` is an **interface**: Type parameter can be `x` or its implementation classes

## Example 1: Bounded Type with Class (T extends Number)

```java
class NumberContainer<T extends Number> {
    private T value;
    
    public NumberContainer(T value) {
        this.value = value;
    }
    
    public void displayValue() {
        System.out.println("Value: " + value);
        System.out.println("Double value: " + value.doubleValue()); // Number method available
    }
    
    public T getValue() {
        return value;
    }
}

class BoundedTypeDemo {
    public static void main(String[] args) {
        // Valid - Integer extends Number
        NumberContainer<Integer> intContainer = new NumberContainer<Integer>(42);
        intContainer.displayValue();
        
        // Valid - Double extends Number
        NumberContainer<Double> doubleContainer = new NumberContainer<Double>(3.14);
        doubleContainer.displayValue();
        
        // Valid - Float extends Number
        NumberContainer<Float> floatContainer = new NumberContainer<Float>(2.5f);
        floatContainer.displayValue();
        
        // COMPILE ERROR - String does not extend Number
        // NumberContainer<String> stringContainer = new NumberContainer<String>("Hello");
    }
}
```

### Output:
```
Value: 42
Double value: 42.0
Value: 3.14
Double value: 3.14
Value: 2.5
Double value: 2.5
```

### Compile Error Case:
```java
// This will cause compile-time error
NumberContainer<String> stringContainer = new NumberContainer<String>("Hello");
// Error: String is not within bounds of type-parameter T
```

## Example 2: Bounded Type with Interface (T extends Runnable)

```java
class TaskContainer<T extends Runnable> {
    private T task;
    
    public TaskContainer(T task) {
        this.task = task;
    }
    
    public void executeTask() {
        System.out.println("Executing task: " + task.getClass().getSimpleName());
        task.run(); // Runnable method available
    }
    
    public T getTask() {
        return task;
    }
}

class MyTask implements Runnable {
    private String name;
    
    public MyTask(String name) {
        this.name = name;
    }
    
    @Override
    public void run() {
        System.out.println("Running task: " + name);
    }
}

class RunnableDemo {
    public static void main(String[] args) {
        // Valid - MyTask implements Runnable
        TaskContainer<MyTask> taskContainer = new TaskContainer<MyTask>(new MyTask("Data Processing"));
        taskContainer.executeTask();
        
        // Valid - Thread implements Runnable
        TaskContainer<Thread> threadContainer = new TaskContainer<Thread>(new Thread(() -> {
            System.out.println("Running in thread");
        }));
        threadContainer.executeTask();
        
        // COMPILE ERROR - String does not implement Runnable
        // TaskContainer<String> stringContainer = new TaskContainer<String>("Hello");
    }
}
```

## Invalid Keyword Usage

### Cannot Use `implements` and `super`

```java
// INVALID - Cannot use implements keyword
// class Test<T implements Runnable> { }

// INVALID - Cannot use super keyword  
// class Test<T super Number> { }
```

**Note**: The purpose of `implements` keyword can be replaced with `extends` keyword in bounded types.

## Alternative Generic Parameter Names

While `T` is conventional, you can use any valid Java identifier:

```java
class Container<Element> {
    private Element data;
    
    public Container(Element data) {
        this.data = data;
    }
    
    public Element getData() {
        return data;
    }
}

class GenericNameDemo {
    public static void main(String[] args) {
        Container<String> stringContainer = new Container<String>("Hello");
        Container<Integer> intContainer = new Container<Integer>(100);
        
        System.out.println("String data: " + stringContainer.getData());
        System.out.println("Integer data: " + intContainer.getData());
    }
}
```

## Multiple Type Parameters

You can define multiple type parameters:

```java
class Pair<First, Second> {
    private First first;
    private Second second;
    
    public Pair(First first, Second second) {
        this.first = first;
        this.second = second;
    }
    
    public First getFirst() { return first; }
    public Second getSecond() { return second; }
    
    public void display() {
        System.out.println("First: " + first + ", Second: " + second);
    }
}

class Triple<X, Y, Z> {
    private X x;
    private Y y; 
    private Z z;
    
    public Triple(X x, Y y, Z z) {
        this.x = x;
        this.y = y;
        this.z = z;
    }
    
    public void displayAll() {
        System.out.println("X: " + x + ", Y: " + y + ", Z: " + z);
    }
}

class MultipleGenericsDemo {
    public static void main(String[] args) {
        // Two type parameters
        Pair<String, Integer> nameAge = new Pair<String, Integer>("John", 25);
        nameAge.display();
        
        Pair<Double, Boolean> scorePass = new Pair<Double, Boolean>(85.5, true);
        scorePass.display();
        
        // Three type parameters
        Triple<String, Integer, Double> studentInfo = new Triple<String, Integer, Double>("Alice", 20, 92.5);
        studentInfo.displayAll();
        
        // HashMap example
        HashMap<String, Integer> map = new HashMap<String, Integer>();
        map.put("Apple", 10);
        map.put("Banana", 5);
        System.out.println("HashMap: " + map);
    }
}
```

## Combination of Bounded Types

### Valid Combinations

#### Example 1: Class + Interface
```java
class Test<T extends Number & Runnable> {
    // T must extend Number AND implement Runnable
}

// Usage example
class NumberTask extends Number implements Runnable {
    private double value;
    
    public NumberTask(double value) {
        this.value = value;
    }
    
    @Override public void run() { System.out.println("Running with value: " + value); }
    @Override public int intValue() { return (int) value; }
    @Override public long longValue() { return (long) value; }
    @Override public float floatValue() { return (float) value; }
    @Override public double doubleValue() { return value; }
}
```

#### Example 2: Multiple Interfaces
```java
interface Drawable { void draw(); }
interface Clickable { void click(); }

class Test<T extends Drawable & Clickable> {
    // T must implement both Drawable AND Clickable
}
```

#### Example 4: Interface + Another Interface
```java
class Test<T extends Runnable & Cloneable> {
    // T must implement both Runnable AND Cloneable (valid)
}
```

### Invalid Combinations

#### Example 3: Multiple Classes (Invalid)
```java
// INVALID - Cannot extend more than one class
// class Test<T extends Number & String> { }
// Compile Error: Cannot specify multiple class bounds
```

#### Example 5: Interface Before Class (Invalid)
```java
// INVALID - Class must come first, then interfaces
// class Test<T extends Runnable & Number> { }
// Correct order: class Test<T extends Number & Runnable> { }
```

## Rules for Bounded Types

1. **Single Class Bound**: Can extend only one class
2. **Multiple Interface Bounds**: Can implement multiple interfaces using `&`
3. **Order Matters**: Class must come first, followed by interfaces
4. **Use `extends` Only**: Cannot use `implements` or `super` keywords
5. **Inheritance Rules Apply**: Type parameter must satisfy all bounds

## Benefits of Bounded Types

- **Method Access**: Can call methods specific to the bound type
- **Type Safety**: Ensures only compatible types are used
- **Code Reusability**: Generic code with specific constraints
- **Compile-Time Checking**: Errors caught during compilation
