# Java Enum Basics

Enums in Java are a powerful feature introduced in Java 1.5. They allow the definition of a fixed set of named constants and provide capabilities beyond traditional enums found in older languages.

---

## What is an Enum?

An `enum` is used to define a group of named constants, each of which is an instance of its enum type.

### Example 1:

```java
enum Month {
  JAN, FEB, MAR, APR, MAY, JUN, JUL, AUG, SEP, OCT, NOV, DEC;
}
```

### Example 2:

```java
enum Beer {
  KF, KO, RC, FO;
}
```

### Key Points:

* The `enum` feature was introduced in Java 1.5.
* Java enums are more powerful than those in many older programming languages.
* Enums can be used to create custom data types.

---

## Internal Implementation of Enums

Internally, enums are implemented using class concepts.

* Each enum constant is:

  * A reference to an object of the enum type.
  * Implicitly `public static final`.

### Example 3:

```java
enum Beer {
  KF, KO;
}
```

**Equivalent Internal Representation:**

```java
final class Beer extends java.lang.Enum<Beer> {
  public static final Beer KF = new Beer();
  public static final Beer KO = new Beer();
}
```

---

## Declaration and Usage of Enums

### Example 4:

```java
enum Beer {
  KF, KO, RC, FO; // Semicolon is optional
}

class Test {
  public static void main(String[] args) {
    Beer b1 = Beer.KF;
    System.out.println(b1);
  }
}
```

**Output:**

```
KF
```

### Notes:

* Enum constants are static, so they can be accessed using the enum name (e.g., `Beer.KF`).
* Each enum implicitly overrides the `toString()` method to return the constant name.

---

## Using Enums with Switch Statements

Prior to Java 1.5, only the following types could be used with `switch`:

* `byte`, `short`, `char`, `int`

From Java 1.5 onwards:

* You can also use their wrapper classes and enums as switch arguments.

---

## Enum with Overridden Methods

Enums in Java can contain methods, including abstract methods that can be overridden by individual constants.

### Example:

```java
enum Color {
  BLUE,
  RED {
    public void info() {
      System.out.println("Dangerous color");
    }
  },
  GREEN;

  public void info() {
    System.out.println("Universal color");
  }
}

class Test {
  public static void main(String[] args) {
    Color[] colors = Color.values();
    for (Color c : colors) {
      c.info();
    }
  }
}
```

**Output:**

```
Universal color
Dangerous color
Universal color
```

---

## enum vs Enum vs Enumeration

### `enum` (keyword):

* A Java keyword used to define a group of named constants.

### `Enum` (class):

* A class in the `java.lang` package.
* All enums in Java are implicitly subclasses of the `Enum` class.
* Acts as the base class for all enums.

### `Enumeration` (interface):

* An interface in the `java.util` package.
* Used to iterate over collections (e.g., through legacy classes like `Vector`).

---

Enums are a robust and essential part of Java programming, enabling clean, type-safe constant definitions and supporting advanced functionality such as methods and constructors.
