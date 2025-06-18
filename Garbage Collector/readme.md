# Garbage Collector in Java

## Introduction

In old languages like C++, the programmer is responsible for both creation and destruction of objects. Usually, the programmer takes great care while creating objects and neglects the destruction of useless objects. Due to this negligence, at a certain point in time, there may not be sufficient memory available for the creation of new objects, and the entire application may crash due to memory problems.

- But in Java, the programmer is responsible only for the creation of new objects and is **not** responsible for the destruction of objects.
- Sun provided an assistant which is always running in the background for destruction of useless objects. Due to this assistant, the chance of a Java program failing because of memory problems is very rare.
- This assistant is nothing but the **Garbage Collector**. Hence, the main objective of GC is to destroy useless objects.

---

## Ways to Make an Object Eligible for Garbage Collection (GC)

Even though the programmer is not responsible for the destruction of objects, it is always a good programming practice to make an object eligible for GC if it is no longer required.

An object is eligible for GC if and only if it does not have any references.

The following are various possible ways to make an object eligible for GC:

---

### 1. Nullifying the Reference Variable

If an object is no longer required, we can make it eligible for GC by assigning `null` to all its reference variables.

```java
Student s1 = new Student();
Student s2 = new Student();
// Here, no objects eligible for GC

s1 = null;
// Here, one object eligible for GC

s2 = null;
// Here, two objects eligible for GC
```

---

### 2. Reassign the Reference Variable

If an object is no longer required, reassign all its reference variables to some other objects. Then, the old object is by default eligible for GC.

```java
Student s1 = new Student();
Student s2 = new Student();
// Here, no objects eligible for GC

s1 = new Student();
// Here, one object eligible for GC

s2 = s1;
// Here, two objects eligible for GC
```

---

### 3. Objects Created Inside a Method

Objects created inside a method are by default eligible for GC once the method completes.

**Example 1:**
```java
class Main {
    public static void main(String[] args){
        methodOne();
        //Here, two objects eligible for GC
    }
    public static void methodOne(){
         Student s1= new Student();
         Student s2= new Student();
    }
}
```

**Example 2:**
```java
class Main {
    public static void main(String[] args){
        Student s1=methodOne();
        //Here, one object eligible for GC
    }
    public static void methodOne(){
         Student s1= new Student();
         Student s2= new Student();
         return s1;
    }
}
```

**Example 3:**
```java
class Main {
    public static void main(String[] args){
        methodOne();
        //Here, two objects eligible for GC
    }
    public static void methodOne(){
         Student s1= new Student();
         Student s2= new Student();
         return s1;
    }
}
```

**Example 4:**
```java
class Main {
    static Student s1;
    public static void main(String[] args){
        methodOne();
        //Here, one object eligible for GC
    }
    public static void methodOne(){
         s1= new Student();
         Student s2= new Student();
         return s1;
    }
}
```

### 4. Island of Isolation

**Note:** If an object doesn't have any reference, then it is always eligible for GC.  
**Note:** Even though an object has references, it can still be eligible for GC in some cases.

#### Island of Isolation

An "island of isolation" occurs when a group of objects reference each other, but none of them are referenced from any active part of the program. All references are internal within the group, so the entire group becomes unreachable and eligible for garbage collection.

#### Example:

```java
class Island{
    Island i;
    public static void main(string[] args){
        Island a=new Island();
        Island b=new Island();
        a.i=b;
        b.i=a;
        // Both objects are now eligible for GC (island of isolation)
    }
}
```

#### Explanation:

- Two objects (`a` and `b`) are created, each referencing the other.
- The only references to these objects from outside are `a` and `b`.
- When both `a` and `b` are set to `null`, there are no external references to these objects.
- Although `a.i` points to `b` and `b.i` points to `a`, these are only internal references.
- Since there are no references from any active part of the program, both objects form an "island of isolation" and are eligible for garbage collection.


# Requesting JVM to Run Garbage Collection (GC)

The following are various ways to request the JVM to run garbage collection:

---

## 1. By System Class

The `System` class contains a static method `gc()` for this purpose.

**Example:**
```java
System.gc();
```

---

## 2. By Runtime Class

- A Java application can communicate with the JVM by using a `Runtime` object.
- The `Runtime` class is a singleton class present in the `java.lang` package.
- We can create a `Runtime` object by using the factory method `getRuntime()`.

**Example:**
```java
Runtime r = Runtime.getRuntime();
```

Once we have a `Runtime` object, we can call the following methods on that object:

- `freeMemory()`: Returns the free memory present in the heap.
- `totalMemory()`: Returns the total memory of the heap.
- `gc()`: Requests the JVM to run garbage collection.

---

### Example Code:

```java
import java.util.Date;

class RuntimeDemo{
    public static void main(string[] args){
        Runtime r=Runtime.getRuntime();
        System.out.println("Total memory in heap: "+r.totalMemory());
        System.out.println("Free memory in heap: "+r.freeMemory());
        for (int i=0;i<1000;i++){
            Date d=new Date();
            d=null;
        }
        System.out.println("free memory of heap: "+r.freeMemory());
        r.gc();
        System.out.println("free memory of heap: "+r.freeMemory());
    }
}
```

**Output:**
```
Total memory of the heap: 5177344
Free memory of the heap: 4994920
Free memory of the heap: 4743408
Free memory of the heap: 5049776
```

---

### Important Notes:
1. **Runtime Class**:
   - The `Runtime` class is a singleton class, so it is not possible to create an object using the constructor.

2. **Valid Ways to Request JVM to Run GC**:
   - `System.gc();` (valid)
   - `Runtime.gc();` (invalid)
   - `(new Runtime).gc();` (invalid)
   - `Runtime.getRuntime().gc();` (valid)

3. **Static vs Instance Method**:
   - The `gc()` method in the `System` class is static, whereas it is an instance method in the `Runtime` class.

4. **Recommendation**:
   - It is recommended to use the `gc()` method from the `System` class over the `Runtime` class.

5. **Limitations in Java**:
   - It is not possible to find the size of an object or the address of an object in Java.
