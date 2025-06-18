# Java Interview Questions - Complete Guide

## Basic Java Concepts

### 1. What is Java and why is it not a pure object-oriented language?

**Answer:** Java is a class-based, object-oriented programming language designed for simplicity and portability. Its key feature is "write once, run anywhere" - code compiled on one platform can run on any platform supporting Java.

**Why not pure OOP:**
- **Primitive data types:** Uses primitives like `int`, `char`, `double` instead of objects
- **Static methods:** Can be called without creating objects, breaking OOP principles

**Example:**
```java
int number = 5; // Primitive, not an object
Math.max(10, 20); // Static method call without object creation
```

### 2. Key Components of Java Program Execution

**JDK (Java Development Kit):** Complete development environment including compiler
**JRE (Java Runtime Environment):** Runtime environment needed to execute Java programs  
**JVM (Java Virtual Machine):** Executes bytecode on specific platforms

**Process:**
1. **Source Code:** `Example.java`
2. **Compilation:** `javac Example.java` → `Example.class` (bytecode)
3. **Execution:** JVM runs the bytecode

### 3. Main Features of Java

- **Simple:** Easy syntax, no pointers
- **Object-Oriented:** Code organization through objects
- **Platform Independent:** "Write once, run anywhere"
- **Secure:** Built-in security features
- **Multi-threaded:** Handle multiple tasks simultaneously
- **Robust:** Strong memory management and error handling

### 4. Java String Pool

**Definition:** Memory area where string literals are stored to optimize memory usage.

**Example:**
```java
String str1 = "Hello"; // Stored in string pool
String str2 = "Hello"; // References same object in pool
String str3 = new String("Hello"); // Stored in heap, not pool

System.out.println(str1 == str2); // true (same reference)
System.out.println(str1 == str3); // false (different memory locations)
```

### 5. Wrapper Classes

**Purpose:** Convert primitive types into objects for additional functionality.

**Examples:**
```java
// Primitive vs Wrapper
int primitive = 10;
Integer wrapper = Integer.valueOf(10);

// Autoboxing and Unboxing
Integer autoBoxed = 15; // int → Integer (autoboxing)
int unboxed = autoBoxed; // Integer → int (unboxing)

// Useful methods
String numberStr = Integer.toString(100);
int parsed = Integer.parseInt("200");
```

### 6. Collections Framework

**Use Case:** Online bookstore management

```java
// List for all books
List<String> allBooks = new ArrayList<>();
allBooks.add("Java Programming");
allBooks.add("Data Structures");

// Set for unique genres
Set<String> genres = new HashSet<>();
genres.add("Programming");
genres.add("Programming"); // Duplicate ignored

// Map for author-to-books mapping
Map<String, List<String>> authorBooks = new HashMap<>();
authorBooks.put("John Doe", Arrays.asList("Book1", "Book2"));
```

## Object-Oriented Programming

### 7. `this` and `super` Keywords

**`this` keyword:** References current object
**`super` keyword:** References parent class

```java
class Animal {
    String name = "Animal";
    
    void makeSound() {
        System.out.println("Animal sound");
    }
}

class Dog extends Animal {
    String name = "Dog";
    
    Dog(String name) {
        this.name = name; // 'this' refers to current object
    }
    
    void makeSound() {
        super.makeSound(); // Calls parent method
        System.out.println("Woof!");
    }
    
    void display() {
        System.out.println("This dog: " + this.name);
        System.out.println("Animal name: " + super.name);
    }
}
```

### 8. Static vs Instance Methods

```java
class Calculator {
    static int count = 0; // Static variable
    int instanceVar = 10; // Instance variable
    
    // Static method - belongs to class
    static int add(int a, int b) {
        return a + b;
    }
    
    // Instance method - belongs to object
    int multiply(int a, int b) {
        return a * b * this.instanceVar;
    }
}

// Usage
Calculator.add(5, 3); // Static method call - no object needed
Calculator calc = new Calculator();
calc.multiply(4, 2); // Instance method call - object required
```

### 9. Constructors

**Types:**
- **Default Constructor:** No parameters
- **Parameterized Constructor:** Takes parameters

```java
class Car {
    String model;
    int year;
    
    // Default constructor
    Car() {
        model = "Unknown";
        year = 2020;
        System.out.println("Default car created");
    }
    
    // Parameterized constructor
    Car(String model, int year) {
        this.model = model;
        this.year = year;
        System.out.println("Car created: " + model);
    }
}

// Usage
Car defaultCar = new Car(); // Calls default constructor
Car tesla = new Car("Tesla Model Y", 2022); // Calls parameterized constructor
```

## String Manipulation

### 10. StringBuffer vs StringBuilder

| Feature | StringBuffer | StringBuilder |
|---------|-------------|---------------|
| Thread Safety | Synchronized (Thread-safe) | Not synchronized |
| Performance | Slower | Faster |
| Use Case | Multi-threaded environments | Single-threaded |

```java
// StringBuffer (Thread-safe)
StringBuffer sb = new StringBuffer("Hello");
sb.append(" World");
System.out.println(sb.toString()); // "Hello World"

// StringBuilder (Faster)
StringBuilder sb2 = new StringBuilder("Hello");
sb2.append(" World");
System.out.println(sb2.toString()); // "Hello World"
```

### 11. Abstract Classes vs Interfaces

| Feature | Abstract Class | Interface |
|---------|---------------|-----------|
| Methods | Abstract + Concrete methods | Only abstract methods (Java 7) |
| Inheritance | Single inheritance | Multiple inheritance |
| Variables | All types allowed | public static final only |
| Implementation | Partial implementation | No implementation |

```java
// Abstract Class
abstract class Shape {
    abstract void draw(); // Abstract method
    
    void move() { // Concrete method
        System.out.println("Shape moved");
    }
}

// Interface
interface Drawable {
    void draw(); // Implicitly abstract
    int DEFAULT_COLOR = 1; // Implicitly public static final
}

class Circle extends Shape implements Drawable {
    void draw() {
        System.out.println("Drawing circle");
    }
}
```

## Polymorphism

### 12. Method Overloading

**Definition:** Multiple methods with same name but different parameters.

```java
class Calculator {
    // Overloaded methods
    int add(int a, int b) {
        return a + b;
    }
    
    double add(double a, double b) {
        return a + b;
    }
    
    int add(int a, int b, int c) {
        return a + b + c;
    }
    
    // Can we overload main method? YES!
    public static void main(int x) {
        System.out.println("Overloaded main with int: " + x);
    }
    
    public static void main(String[] args) {
        System.out.println("Original main method");
        main(42); // Calling overloaded main
    }
}
```

### 13. Method Overriding

**Definition:** Subclass provides specific implementation of parent class method.

```java
class Animal {
    void move() {
        System.out.println("Animal moves");
    }
    
    void eat() {
        System.out.println("Animal eats");
    }
}

class Dog extends Animal {
    @Override
    void move() { // Overriding parent method
        System.out.println("Dog runs");
    }
    
    // Cannot override static or private methods
    // static methods belong to class, not instance
    // private methods are not inherited
}
```

## Exception Handling

### 14. Exception Handling Mechanism

```java
public class ExceptionExample {
    public static void main(String[] args) {
        try {
            int result = 10 / 0; // May throw exception
            System.out.println("Result: " + result);
        } catch (ArithmeticException e) {
            System.out.println("Cannot divide by zero!");
        } finally {
            System.out.println("This block always executes");
        }
    }
}
```

**Flow:**
1. Code in `try` block executes
2. If exception occurs → `catch` block handles it
3. `finally` block always executes (cleanup code)

## Advanced Concepts

### 15. Thread Lifecycle

**States:**
1. **NEW:** Thread created but not started
2. **RUNNABLE:** Ready to run or running
3. **BLOCKED/WAITING:** Waiting for resources
4. **TERMINATED:** Thread finished execution

```java
class MyThread extends Thread {
    public void run() {
        System.out.println("Thread is running");
    }
}

MyThread thread = new MyThread(); // NEW state
thread.start(); // RUNNABLE state
```

### 16. Singleton Pattern

**Purpose:** Ensure only one instance of a class exists.

```java
class Singleton {
    private static Singleton instance;
    
    // Private constructor prevents direct instantiation
    private Singleton() {}
    
    // Global access point
    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}

// Usage
Singleton obj1 = Singleton.getInstance();
Singleton obj2 = Singleton.getInstance();
System.out.println(obj1 == obj2); // true - same instance
```

### 17. Aggregation vs Composition

**Aggregation (HAS-A):** Objects can exist independently
**Composition (PART-OF):** Child objects depend on parent

```java
// Aggregation Example
class Department {
    private List<Student> students;
    // Students can exist without department
}

// Composition Example  
class Library {
    private List<Book> books;
    // Books cannot exist without library
    // If library is destroyed, books are also destroyed
}
```

### 18. Anonymous Inner Classes

**Definition:** Class without name, defined and instantiated at once.

```java
interface Greeting {
    void sayHello();
}

public class AnonymousExample {
    public static void main(String[] args) {
        // Anonymous inner class
        Greeting greeting = new Greeting() {
            @Override
            public void sayHello() {
                System.out.println("Hello from anonymous class!");
            }
        };
        
        greeting.sayHello();
    }
}
```

## Type Conversion and Keywords

### 19. Type Conversion

**Implicit (Automatic):** Smaller → Larger data type
**Explicit (Manual):** Larger → Smaller data type (casting required)

```java
// Implicit conversion
int intValue = 100;
double doubleValue = intValue; // int → double (automatic)

// Explicit conversion  
double bigNumber = 3.14159;
int intNumber = (int) bigNumber; // double → int (manual casting)
System.out.println(intNumber); // Output: 3 (decimal part lost)
```

### 20. Volatile Keyword

**Purpose:** Ensures variable changes are immediately visible to all threads.

```java
class SharedCounter {
    private volatile int counter = 0; // volatile ensures thread visibility
    
    public void increment() {
        counter++; // All threads see updated value immediately
    }
    
    public int getCounter() {
        return counter;
    }
}
```

### 21. System Streams

```java
// Standard output
System.out.println("Regular message");

// Error output  
System.err.println("Error message");

// Standard input
Scanner scanner = new Scanner(System.in);
System.out.print("Enter your name: ");
String name = scanner.nextLine();
```

### 22. Access Modifiers

| Modifier | Class | Package | Subclass | World |
|----------|-------|---------|----------|-------|
| public | ✓ | ✓ | ✓ | ✓ |
| protected | ✓ | ✓ | ✓ | ✗ |
| default | ✓ | ✓ | ✗ | ✗ |
| private | ✓ | ✗ | ✗ | ✗ |

```java
class AccessExample {
    public int publicVar = 1;        // Accessible everywhere
    protected int protectedVar = 2;   // Package + subclasses
    int defaultVar = 3;              // Package only
    private int privateVar = 4;      // Class only
}
```

### 23. final, finally, finalize

```java
// final - restricts modification/inheritance
final int CONSTANT = 100; // Cannot be reassigned
final class FinalClass {} // Cannot be extended

// finally - always executes in try-catch
try {
    // risky code
} finally {
    // cleanup code - always runs
}

// finalize - called by garbage collector (deprecated)
@Override
protected void finalize() {
    // cleanup before object destruction
}
```

## Programming Problems

### 24. Toggle Case Program

```java
public static String toggleCase(String input) {
    StringBuilder result = new StringBuilder();
    
    for (char ch : input.toCharArray()) {
        if (Character.isUpperCase(ch)) {
            result.append(Character.toLowerCase(ch));
        } else if (Character.isLowerCase(ch)) {
            result.append(Character.toUpperCase(ch));
        } else {
            result.append(ch); // Keep non-alphabetic characters
        }
    }
    
    return result.toString();
}

// Usage
String original = "Hello World!";
String toggled = toggleCase(original);
System.out.println(toggled); // Output: hELLO wORLD!
```

### 25. Count Digit Occurrences

```java
public static int countDigitOccurrences(int number, int digit) {
    int count = 0;
    number = Math.abs(number); // Handle negative numbers
    
    if (number == 0 && digit == 0) return 1;
    
    while (number > 0) {
        if (number % 10 == digit) {
            count++;
        }
        number /= 10;
    }
    
    return count;
}

// Usage
int number = 123445;
int digit = 4;
int occurrences = countDigitOccurrences(number, digit);
System.out.println("Digit " + digit + " occurs " + occurrences + " times");
// Output: Digit 4 occurs 2 times
```

### 26. Reverse Array Using Recursion

```java
public static void reverseArray(int[] arr, int start, int end) {
    // Base case
    if (start >= end) {
        return;
    }
    
    // Swap elements
    int temp = arr[start];
    arr[start] = arr[end];
    arr[end] = temp;
    
    // Recursive call
    reverseArray(arr, start + 1, end - 1);
}

// Usage
int[] array = {1, 2, 3, 4, 5};
reverseArray(array, 0, array.length - 1);
System.out.println(Arrays.toString(array)); // Output: [5, 4, 3, 2, 1]
```

### 27. Check Anagram Strings

```java
public static boolean areAnagrams(String str1, String str2) {
    // Remove spaces and convert to lowercase
    str1 = str1.replaceAll("\\s", "").toLowerCase();
    str2 = str2.replaceAll("\\s", "").toLowerCase();
    
    // Check lengths
    if (str1.length() != str2.length()) {
        return false;
    }
    
    // Convert to char arrays and sort
    char[] arr1 = str1.toCharArray();
    char[] arr2 = str2.toCharArray();
    
    Arrays.sort(arr1);
    Arrays.sort(arr2);
    
    // Compare sorted arrays
    return Arrays.equals(arr1, arr2);
}

// Usage
String word1 = "listen";
String word2 = "silent";
boolean isAnagram = areAnagrams(word1, word2);
System.out.println(word1 + " and " + word2 + " are anagrams: " + isAnagram);
// Output: listen and silent are anagrams: true
```

### 28. Find First and Last Occurrence

```java
public static void findFirstAndLastOccurrence(int[] arr, int target) {
    int firstIndex = -1;
    int lastIndex = -1;
    
    // Find first and last occurrence in single pass
    for (int i = 0; i < arr.length; i++) {
        if (arr[i] == target) {
            if (firstIndex == -1) {
                firstIndex = i; // First occurrence
            }
            lastIndex = i; // Update last occurrence
        }
    }
    
    if (firstIndex == -1) {
        System.out.println("Element not found in array");
    } else {
        System.out.println("First occurrence at index: " + firstIndex);
        System.out.println("Last occurrence at index: " + lastIndex);
    }
}

// Usage
int[] array = {1, 2, 3, 2, 4, 2, 5};
findFirstAndLastOccurrence(array, 2);
// Output: First occurrence at index: 1, Last occurrence at index: 5
```

## Memory Management

### 29. String Immutability Benefits

**Benefits beyond security:**

1. **String Pool Optimization:** Memory sharing of identical strings
2. **Thread Safety:** Immutable objects are inherently thread-safe
3. **Reliable Hash Codes:** Safe for use as HashMap keys

```java
// String pool example
String str1 = "Hello";
String str2 = "Hello";
String str3 = new String("Hello");

System.out.println(str1 == str2); // true (same reference in pool)
System.out.println(str1 == str3); // false (str3 in heap)
System.out.println(str1.equals(str3)); // true (same content)
```

### 30. Deep Copy vs Shallow Copy

```java
class Person {
    String name;
    
    Person(String name) {
        this.name = name;
    }
}

// Shallow Copy Example
Person person1 = new Person("John");
Person person2 = person1; // Shallow copy - same reference
person2.name = "Jane";
System.out.println(person1.name); // Output: "Jane" (both affected)

// Deep Copy Example  
Person person3 = new Person("Bob");
Person person4 = new Person(person3.name); // Deep copy - new object
person4.name = "Alice";
System.out.println(person3.name); // Output: "Bob" (original unchanged)
```

## Summary

This guide covers 30 essential Java interview questions ranging from basic concepts to advanced programming problems. Key areas include:

- **Core Java:** OOP principles, data types, string handling
- **Advanced Concepts:** Threading, design patterns, memory management  
- **Programming Problems:** Common coding challenges with solutions
- **Best Practices:** Code organization, exception handling, performance optimization

Practice these concepts with hands-on coding to solidify your understanding for Java interviews!
