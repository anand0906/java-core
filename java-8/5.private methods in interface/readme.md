# **Private Methods in Interface**

### Need for Private Methods in Interface:
If multiple default methods share common functionality, redundant code may increase the length of the code, reduce readability, and create maintenance issues. Java 8 did not provide a solution for this problem.

#### Example (Before Java 9 - Without Private Methods):
```java
public interface Java8DBLogging {
    // Abstract Methods List
    default void logInfo(String message) {
        // Step 1: Connect to Database
        // Step 2: Log Info Message
        // Step 3: Close the Database Connection
    }
    
    default void logWarn(String message) {
        // Step 1: Connect to Database
        // Step 2: Log Warn Message
        // Step 3: Close the Database Connection
    }
}
```
In the above code, all log methods contain duplicate steps, which increase redundancy and make maintenance difficult.

### Introduction of Private Methods in Java 9:
Java 9 introduced private methods inside interfaces to eliminate redundancy. We can now separate the common code into a private method and call it from each default method when needed.

#### Example (Using Private Methods in Java 9):
```java
public interface Java9DBLogging {
    // Abstract Methods List
    default void logInfo(String message) {
        log(message, "INFO");
    }
    
    default void logWarn(String message) {
        log(message, "WARN");
    }
    
    private void log(String msg, String logLevel) {
        // Step 1: Connect to Database
        // Step 2: Log Message with the provided logLevel
        // Step 3: Close the Database Connection
    }
}
```

### Private Instance Methods in Interface:
Private instance methods enhance code reusability for default methods.

#### Example:
```java
interface Java9Interf {
    default void m1() {
        m3();
    }
    
    default void m2() {
        m3();
    }
    
    private void m3() {
        System.out.println("Common functionality of methods m1 & m2");
    }
}

class Test implements Java9Interf {
    public static void main(String[] args) {
        Test t = new Test();
        t.m1();
        t.m2();
        // t.m3(); // Compile-time Error
    }
}
```
**Output:**
```
Common functionality of methods m1 & m2
Common functionality of methods m1 & m2
```

### Private Static Methods in Interface:
Private static methods allow code reusability for public static methods.

#### Example:
```java
interface Java9Interf {
    public static void m1() {
        m3();
    }
    
    public static void m2() {
        m3();
    }
    
    private static void m3() {
        System.out.println("Common functionality of methods m1 & m2");
    }
}

class Test {
    public static void main(String[] args) {
        Java9Interf.m1();
        Java9Interf.m2();
    }
}
```
**Output:**
```
Common functionality of methods m1 & m2
Common functionality of methods m1 & m2
```

**Note:** Interface static methods must be called using the interface name, even in implementation classes.

### Advantages of Private Methods in Interfaces:
1. **Code Reusability** – Reduces redundancy by grouping common functionality.
2. **Encapsulation** – Exposes only the intended methods to API clients (implementation classes) since private methods are not visible to them.

### Important Notes:
1. Private methods **cannot be abstract**, and they must have a body.
2. Private methods inside an interface can be either **static** or **non-static**.

### JDK 7 vs JDK 8 vs JDK 9:
| Feature | JDK 7 | JDK 8 | JDK 9 |
|---------|-------|-------|-------|
| Public Static Final Variables | ✅ | ✅ | ✅ |
| Public Abstract Methods | ✅ | ✅ | ✅ |
| Default Methods | ❌ | ✅ | ✅ |
| Public Static Methods | ❌ | ✅ | ✅ |
| Private Instance Methods | ❌ | ❌ | ✅ |
| Private Static Methods | ❌ | ❌ | ✅ |

**Conclusion:**
- Java 8 introduced **default and static methods** inside interfaces.
- Java 9 introduced **private methods**, which improved code reusability without affecting implementation classes.

