# Default Methods in Interfaces

### Introduction
Until Java 7, interfaces could contain only **public abstract methods** and **public static final variables** by default. However, from Java 8 onwards, **default methods** (also known as **defender methods**) were introduced. These methods allow interfaces to have concrete method implementations.

---

### Declaring a Default Method
A **default method** is declared using the `default` keyword in an interface.

#### Syntax:
```java
interface Interf {
    default void m1() {
        System.out.println("Default Method");
    }
}
```

#### Example:
```java
interface Interf {
    default void m1() {
        System.out.println("Default Method");
    }
}

class Test implements Interf {
    public static void main(String[] args) {
        Test t = new Test();
        t.m1();
    }
}
```
**Output:**
```
Default Method
```

---

### Key Points about Default Methods
1. **Backward Compatibility**: Default methods allow us to add new functionality to interfaces without affecting existing implementing classes.
2. **Defender Methods**: Also known as **virtual extension methods**.
3. **Available to Implementation Classes**: These methods are inherited by all implementing classes unless explicitly overridden.
4. **Cannot Override Object Class Methods**: Attempting to override `hashCode()`, `equals()`, or `toString()` in an interface using a default method will result in a **compilation error**.

#### Example:
```java
interface Interf {
    default int hashCode() {  // Compilation error
        return 10;
    }
}
```

---

### Default Methods & Multiple Inheritance (Diamond Problem)
If two interfaces contain a **default method** with the same signature, an ambiguity problem arises when a class implements both interfaces.

#### Example:
```java
interface Left {
    default void m1() {
        System.out.println("Left Default Method");
    }
}

interface Right {
    default void m1() {
        System.out.println("Right Default Method");
    }
}

class Test implements Left, Right {
    public void m1() {
        System.out.println("Test Class Method"); // Overriding required
    }

    public static void main(String[] args) {
        Test t = new Test();
        t.m1();
    }
}
```
**Output:**
```
Test Class Method
```
To resolve ambiguity, we **must override** the method in the implementing class or explicitly specify which interface method to use:
```java
class Test implements Left, Right {
    public void m1() {
        Left.super.m1();  // Calling specific interface method
    }
}
```

---

### Interface with Default Methods vs Abstract Class

| Feature | Interface (with Default Methods) | Abstract Class |
|---------|----------------------------------|---------------|
| Variables | Only `public static final` constants | Can have instance variables |
| State of Object | Does not maintain state | Can maintain object state |
| Constructors | Cannot have constructors | Can have constructors |
| Blocks | No instance/static blocks | Can have instance/static blocks |
| Functional Interface | Can be used with lambda expressions | Cannot use lambda expressions |
| Object Methods | Cannot override `Object` class methods | Can override `Object` class methods |

**Conclusion**: **An interface with default methods is not equivalent to an abstract class!**

---

### Summary
- Default methods in interfaces were introduced in **Java 8**.
- They allow interfaces to **provide concrete method implementations**.
- Useful for **backward compatibility** without affecting existing implementing classes.
- **Cannot override Object class methods** like `hashCode()`, `equals()`, etc.
- **Multiple interface inheritance** requires explicit method overriding to avoid conflicts.

---

# Static Methods in Interface

### Introduction
From **Java 8** onwards, in addition to **default methods**, we can also define **static methods** inside interfaces to implement **utility functions**. Unlike default methods, interface static methods are **not inherited** by implementing classes.

---

### Declaring a Static Method in an Interface
Static methods inside an interface should be accessed using the **interface name** and cannot be called through implementing class references.

#### Example:
```java
interface Interf {
    public static void sum(int a, int b) {
        System.out.println("The Sum: " + (a + b));
    }
}

class Test implements Interf {
    public static void main(String[] args) {
        Test t = new Test();
        t.sum(10, 20); // Compilation Error
        Test.sum(10, 20); // Compilation Error
        Interf.sum(10, 20); // Correct way to call static method
    }
}
```
**Output:**
```
The Sum: 30
```

---

### Key Characteristics of Static Methods in Interfaces
1. **Not Inherited**: Interface static methods **cannot be inherited** by implementing classes.
2. **Access via Interface Name**: They must be **invoked using the interface name**.
3. **Overriding Not Applicable**: Static methods cannot be overridden by implementing classes.

#### Example 1: Same Method in Implementation Class (Valid but Not Overriding)
```java
interface Interf {
    public static void m1() {}
}

class Test implements Interf {
    public static void m1() {} // Valid, but NOT overriding
}
```

#### Example 2: Instance Method with Same Name (Valid but Not Overriding)
```java
interface Interf {
    public static void m1() {}
}

class Test implements Interf {
    public void m1() {} // Valid, but NOT overriding
}
```

---

### Running a Java Interface Directly
From **Java 8**, we can include a **main() method** inside an interface and execute it directly from the command prompt.

#### Example:
```java
interface Interf {
    public static void main(String[] args) {
        System.out.println("Interface Main Method");
    }
}
```

#### Running from Command Prompt:
```
javac Interf.java
java Interf
```
**Output:**
```
Interface Main Method
```

---

### Summary
- **Static methods** inside interfaces allow defining **utility methods**.
- **Not inherited** by implementing classes.
- **Must be called using the interface name**.
- **Cannot be overridden**, but implementing classes can define a method with the same name.
- **Main method** inside an interface enables direct execution.

---




