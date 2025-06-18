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
