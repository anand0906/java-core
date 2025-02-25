# **Set in Java**

## **Introduction**

- `Set` is a **child interface** of `Collection`.
- Used to store a **group of unique objects** (duplicates are **not allowed**).
- **Insertion order is not preserved**.
- `Set` does not introduce new methods; it relies on the `Collection` interface methods.

---

## **Set Hierarchy**

```
                Collection(I)
                     |
                    Set(I) (1.2V)
                     |
       ---------------------------------
       |                               |
    HashSet (1.2V)                SortedSet(I) (1.2V)
       |                               |
 LinkedHashSet (1.4V)          NavigableSet(I) (1.6V)
                                     |
                                 TreeSet (1.2V)
```

---

## **HashSet**

- The underlying **data structure** is a **HashTable**.
- **Duplicates are not allowed**.
- **Insertion order is not preserved** (determined by object `hashCode()`).
- **Allows null insertion** (only **once** due to uniqueness constraint).
- **Heterogeneous objects** are allowed.
- Implements `Serializable`, `Cloneable` but **not** `RandomAccess`.
- **Best suited for search operations**.

> **Note:** If a duplicate element is added, `add()` returns `false` instead of throwing an error.

### **Example: HashSet Behavior**

```java
import java.util.*;

public class HashSetDemo {
    public static void main(String[] args) {
        HashSet<String> h = new HashSet<>();
        System.out.println(h.add("A")); // Output: true
        System.out.println(h.add("A")); // Output: false
    }
}
```

---

## **Constructors of HashSet**

| Constructor                                      | Description                                                                           |
| ------------------------------------------------ | ------------------------------------------------------------------------------------- |
| `HashSet()`                                      | Creates an empty `HashSet` with **default capacity** `16` and **load factor** `0.75`. |
| `HashSet(int initialCapacity)`                   | Creates a `HashSet` with a **specified capacity** and **default load factor** `0.75`. |
| `HashSet(int initialCapacity, float loadFactor)` | Creates a `HashSet` with **specified capacity and load factor**.                      |
| `HashSet(Collection c)`                          | Creates a `HashSet` containing all elements from the given collection.                |

### **Load Factor (Fill Ratio)**

- The **load factor** determines **when** a new `HashSet` object is created.
- Default **load factor = 0.75**, meaning when **75% full, the HashSet resizes**.

---

## **Example: HashSet in Action**

```java
import java.util.*;

public class SetTesting {
    public static void main(String[] args) {
        HashSet<Object> h = new HashSet<>();
        h.add("B");
        h.add("C");
        h.add("D");
        h.add("Z");
        h.add(null);
        h.add(10);
        System.out.println(h.add("Z")); // Output: false (duplicate)
        System.out.println(h); // Output: [B, C, D, Z, null, 10] (unordered)
    }
}
```

---

## **Key Takeaways**

- `HashSet` is **best suited for search operations**.
- **Duplicates are ignored** (no errors, `add()` returns `false`).
- **Insertion order is not maintained**.
- Uses a **HashTable** for storage.

# **LinkedHashSet in Java**

## **Introduction**
- `LinkedHashSet` is a **child class of HashSet**.
- It behaves the same as `HashSet` **except for the following differences**:

| Feature | HashSet | LinkedHashSet |
|---------|--------|--------------|
| **Underlying Data Structure** | HashTable | Combination of **LinkedList + HashTable** |
| **Insertion Order** | **Not preserved** | **Preserved** |
| **Introduced in** | Java 1.2 | Java 1.4 |

### **Usage**
- `LinkedHashSet` is useful for **cache-based applications** where **duplicates are not allowed** and **insertion order should be preserved**.

---
# **SortedSet in Java**

## **Introduction**
- `SortedSet` is a **child interface of Set**.
- Used when elements need to be stored **in a sorted order without duplicates**.
- Defines the following specific methods:

| Method | Description |
|--------|-------------|
| `Object first()` | Returns the **first element** of the `SortedSet`. |
| `Object last()` | Returns the **last element** of the `SortedSet`. |
| `SortedSet headSet(Object obj)` | Returns elements **less than** `obj`. |
| `SortedSet tailSet(Object obj)` | Returns elements **greater than or equal to** `obj`. |
| `SortedSet subSet(Object obj1, Object obj2)` | Returns elements **between `obj1` (inclusive) and `obj2` (exclusive)**. |
| `Comparator comparator()` | Returns the **Comparator** used for sorting (returns `null` if default natural sorting is used). |

### **Example**
```java
Set<Integer> set = new TreeSet<>(Arrays.asList(100, 101, 104, 106, 110, 115, 120));
System.out.println(set.first()); // 100
System.out.println(set.last());  // 120
System.out.println(set.headSet(106)); // [100, 101, 104]
System.out.println(set.tailSet(106)); // [106, 110, 115, 120]
System.out.println(set.subSet(101, 115)); // [101, 104, 106, 110]
```

---
# **TreeSet in Java**

## **Introduction**
- The underlying data structure of `TreeSet` is a **Balanced Tree**.
- **Duplicates are not allowed**.
- **Insertion order is not preserved**.
- **Heterogeneous objects are not allowed** (throws `ClassCastException`).
- **Null insertion is allowed only once**.
- Implements `Serializable` and `Cloneable` but **not** `RandomAccess`.
- Elements are inserted in either **default natural sorting order** or **customized sorting order**.

---
## **Constructors in TreeSet**

| Constructor | Description |
|------------|-------------|
| `TreeSet()` | Creates an empty `TreeSet` with **default natural sorting order**. |
| `TreeSet(Comparator c)` | Creates a `TreeSet` with a **customized sorting order** defined by a `Comparator`. |
| `TreeSet(Collection c)` | Creates a `TreeSet` containing all elements from the given collection. |
| `TreeSet(SortedSet s)` | Creates a `TreeSet` initialized from an existing `SortedSet`. |

---
## **Example: TreeSet in Action**
```java
import java.util.*;

public class SetTesting {
    public static void main(String[] args) {
        TreeSet<String> t = new TreeSet<>();
        t.add("A");
        t.add("a");
        t.add("B");
        t.add("Z");
        t.add("L");
        // t.add(new Integer(10)); // ClassCastException
        // t.add(null); // NullPointerException (Java 1.7+)
        System.out.println(t); // [A, B, L, Z, a]
    }
}
```

---
## **Null Handling in TreeSet**
| Scenario | Allowed? |
|----------|---------|
| Inserting `null` into an **empty TreeSet** (Java 1.6 or lower) | ✅ Yes |
| Inserting `null` into an **empty TreeSet** (Java 1.7+) | ❌ No (throws `NullPointerException`) |
| Inserting `null` into a **non-empty TreeSet** | ❌ No (throws `NullPointerException`) |

### **Example: ClassCastException in TreeSet**
```java
import java.util.*;

public class SetTesting {
    public static void main(String[] args) {
        TreeSet t = new TreeSet();
        t.add(new StringBuffer("A"));
        t.add(new StringBuffer("Z"));
        t.add(new StringBuffer("L"));
        t.add(new StringBuffer("B"));
        System.out.println(t);
    }
}
```

### **Output:**
```
Exception in thread "main" java.lang.ClassCastException: java.lang.StringBuffer
```

### **Why?**
- Default **natural sorting order** requires objects to be **homogeneous and comparable**.
- `StringBuffer` **does not implement** `Comparable`, hence it throws `ClassCastException`.
- `String` and **wrapper classes** already implement `Comparable`, so they work fine.

---
## **Key Takeaways**
- `LinkedHashSet` is similar to `HashSet`, but it **preserves insertion order**.
- `SortedSet` ensures **sorted order** but does not allow duplicates.
- `TreeSet` internally uses a **Balanced Tree**, does **not allow heterogeneous objects**, and follows **natural or custom sorting order**.
- `null` insertion is **not allowed in TreeSet** from Java **1.7 onwards**.

---

# **Comparison of Set Implementations in Java**

## **Overview**
- `Set` is a **collection that does not allow duplicates**.
- It is used to store unique elements in an **unordered or sorted manner**, depending on the implementation.

---
## **Comparison Table: HashSet vs LinkedHashSet vs TreeSet**

| Feature | HashSet | LinkedHashSet | TreeSet |
|---------|--------|--------------|---------|
| **Underlying Data Structure** | HashTable | LinkedList + HashTable | Balanced Tree (Red-Black Tree) |
| **Insertion Order** | ❌ Not preserved | ✅ Preserved | ❌ Not preserved (Sorted Order) |
| **Sorting Order** | ❌ No sorting | ❌ No sorting | ✅ Natural or Custom Sorting |
| **Duplicates Allowed** | ❌ No | ❌ No | ❌ No |
| **Null Allowed?** | ✅ Yes (only once) | ✅ Yes (only once) | ❌ No (Java 1.7+) |
| **Heterogeneous Objects** | ✅ Yes | ✅ Yes | ❌ No (Throws `ClassCastException`) |
| **Performance (Best Case - O(1))** | 🚀 Fastest (O(1) for add, remove, contains) | ⚡ Fast (O(1)) | 🐢 Slowest (O(log n)) |
| **Use Case** | Fast lookups, no order needed | Cache-based apps where insertion order matters | Sorted unique elements |

---




