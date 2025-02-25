# **List in Detail**

## **Hierarchy Overview**

```
                Collection(I)(1.2V)
                      │
                      │
                    List(I)(1.2V)
       ───────────────────────────────────
       │               │               │
       │               │               │
  ArrayList(1.2V)  LinkedList(1.2V)  Vector(1.0V)
                                      │
                                      │ Legacy class
                                      │
                                    Stack
```

---

## **Introduction to ArrayList**

- The underlying data structure of `ArrayList` is a **resizable (growable) array**.
- Allows **duplicate** elements.
- Preserves **insertion order**.
- Can store **heterogeneous objects** (except in `TreeSet` and `TreeMap`).
- Allows **null** insertions.

---

## **Constructors in ArrayList**

| Constructor                      | Description                                                                   |
| -------------------------------- | ----------------------------------------------------------------------------- |
| `ArrayList()`                    | Creates an empty `ArrayList` with a default initial capacity of **10**.       |
| `ArrayList(int initialCapacity)` | Creates an empty `ArrayList` with a **specified** initial capacity.           |
| `ArrayList(Collection c)`        | Creates an `ArrayList` containing all elements from the specified collection. |

> **Dynamic Growth Formula**:
>
> - When the array reaches its maximum capacity, it expands using the formula:
>   ```
>   new capacity = (current capacity * 3/2) + 1
>   ```

---

## **Example of ArrayList Usage**

```java
import java.util.*;

public class ArrayListDemo {
    public static void main(String[] args) {
        ArrayList list = new ArrayList<>();
        System.out.println("Initial size: " + list.size());

        list.add("A");
        list.add(10);
        list.add("A");
        list.add(null);
        System.out.println("After adding elements: " + list);

        list.remove(2);
        System.out.println("After removing index 2: " + list);

        list.add(2, "M");
        list.add("N");
        System.out.println("Final List: " + list);
    }
}
```

**Output:**

```
Initial size: 0
After adding elements: [A, 10, A, null]
After removing index 2: [A, 10, null]
Final List: [A, 10, M, null, N]
```

---

## **Serializable and Cloneable in Collections**

- Collections are mainly used for **storing and transferring objects**.
- To support this, every collection class implements:
  - **Serializable** (for object serialization)
  - **Cloneable** (for object cloning)

---

## **RandomAccess Interface**

- `ArrayList` and `Vector` implement **RandomAccess**, a marker interface that allows **fast retrieval**.
- **Best Use Case:** When retrieval operations are frequent.
- **Worst Use Case:** When insertions/deletions in the middle are frequent (use `LinkedList` instead).

---

## **Difference Between ArrayList and Vector**

| Feature         | ArrayList                 | Vector                  |
| --------------- | ------------------------- | ----------------------- |
| Synchronization | ❌ Non-Synchronized        | ✅ Synchronized          |
| Performance     | 🚀 Faster (no locking)    | 🐢 Slower (thread-safe) |
| Thread Safety   | ❌ Not thread-safe         | ✅ Thread-safe           |
| Introduced in   | Java **1.2** (Non-Legacy) | Java **1.0** (Legacy)   |

---

## **How to Get a Synchronized Version of ArrayList?**

- By default, `ArrayList` is **not synchronized**.
- Use `Collections.synchronizedList()` to get a synchronized version.

```java
import java.util.*;

public class SynchronizedArrayListExample {
    public static void main(String[] args) {
        ArrayList<String> list = new ArrayList<>();
        List<String> syncList = Collections.synchronizedList(list);
    }
}
```

**Similarly, we can create synchronized versions of:**

```java
public static Set synchronizedSet(Set s);
public static Map synchronizedMap(Map m);
```

---

# **LinkedList in Java**

## **Introduction**
- The underlying data structure of `LinkedList` is a **Doubly Linked List**.
- **Preserves insertion order**.
- Allows **duplicate** and **heterogeneous objects**.
- Supports **null insertion**.
- Implements `Serializable` and `Cloneable` interfaces but **not** `RandomAccess`.
- Best choice for **frequent insertions and deletions in the middle**.
- Worst choice for **retrieval operations** due to sequential access.

---
## **Constructors**
| Constructor | Description |
|------------|-------------|
| `LinkedList()` | Creates an empty `LinkedList` object. |
| `LinkedList(Collection c)` | Creates a `LinkedList` containing all elements from the given collection. |

---
## **LinkedList Class Specific Methods**
The `LinkedList` class provides additional methods for stack and queue operations:

| Method | Description |
|--------|-------------|
| `void addFirst(Object a)` | Adds an element at the beginning. |
| `void addLast(Object a)` | Adds an element at the end. |
| `Object getFirst()` | Retrieves the first element. |
| `Object getLast()` | Retrieves the last element. |
| `Object removeFirst()` | Removes the first element. |
| `Object removeLast()` | Removes the last element. |

---
## **Example: LinkedList in Action**
```java
import java.util.*;

public class LinkedListDemo {
    public static void main(String[] args) {
        LinkedList l = new LinkedList();
        l.add("Durga");
        l.add(30);
        l.add(null);
        l.add("Durga");
        
        l.set(0, "Software");
        l.set(0, "Venky");
        l.removeLast();
        l.addFirst("CCC");
        
        System.out.println(l);
    }
}
```
**Output:**
```
[CCC, Venky, Software, 30, null]
```

---
## **Difference Between ArrayList and LinkedList**

| Feature | ArrayList | LinkedList |
|---------|----------|------------|
| **Best for** | Retrieval operations | Insertions and deletions in the middle |
| **Worst for** | Insertions and deletions (requires shifting elements) | Retrieval operations (sequential access) |
| **Memory Storage** | Elements stored in **contiguous memory locations** | Elements stored in **non-contiguous memory locations** |

# **Vector in Java**

## **Introduction**
- The underlying data structure of `Vector` is a **resizable (growable) array**.
- **Preserves insertion order**.
- Allows **duplicate** and **heterogeneous objects**.
- Supports **null insertion**.
- Implements `Serializable`, `Cloneable`, and `RandomAccess` interfaces.
- All methods in `Vector` are **synchronized**, making it **thread-safe**.

---
## **Constructors in Vector**

| Constructor | Description |
|------------|-------------|
| `Vector()` | Creates an empty vector with an initial capacity of **10**. When full, the capacity doubles. |
| `Vector(int initialCapacity)` | Creates a vector with a **specified** initial capacity. |
| `Vector(int initialCapacity, int incrementalCapacity)` | Creates a vector with specified initial and incremental capacity. |
| `Vector(Collection c)` | Creates a vector containing all elements from the given collection. Useful for collection conversions. |

> **Dynamic Growth Formula**:
> - When the vector reaches its maximum capacity, it expands using the formula:
>   ```
>   new capacity = current capacity * 2
>   ```

---
## **Vector-Specific Methods**

### **1. Adding Elements**
| Method | Description |
|--------|-------------|
| `add(Object o)` | Adds an object (Collection) |
| `add(int index, Object o)` | Adds an object at a specific index (List) |
| `addElement(Object o)` | Adds an element (Vector) |

### **2. Removing Elements**
| Method | Description |
|--------|-------------|
| `remove(Object o)` | Removes an object (Collection) |
| `removeElement(Object o)` | Removes an element (Vector) |
| `remove(int index)` | Removes an element at a specific index (List) |
| `removeElementAt(int index)` | Removes an element at a given index (Vector) |
| `clear()` | Clears all elements (Collection) |
| `removeAllElements()` | Removes all elements (Vector) |

### **3. Retrieving Elements**
| Method | Description |
|--------|-------------|
| `Object get(int index)` | Retrieves an element at a given index (List) |
| `Object elementAt(int index)` | Retrieves an element at a given index (Vector) |
| `Object firstElement()` | Retrieves the first element (Vector) |
| `Object lastElement()` | Retrieves the last element (Vector) |

### **4. Other Utility Methods**
| Method | Description |
|--------|-------------|
| `int size()` | Returns the number of elements in the vector. |
| `int capacity()` | Returns the current capacity of the vector. |
| `Enumeration elements()` | Returns an enumeration of elements in the vector. |

---
## **Example: Vector in Action**
```java
import java.util.*;

public class Main {
    public static void main(String[] args) {
        Vector v = new Vector();
        System.out.println("Initial capacity: " + v.capacity()); // Output: 10
        
        for (int i = 0; i < 10; i++) {
            v.addElement(i);
        }
        System.out.println("Capacity after adding 10 elements: " + v.capacity()); // Output: 10
        
        v.addElement("A");
        System.out.println("Capacity after exceeding limit: " + v.capacity()); // Output: 20
        System.out.println(v); // Output: [0,1,2,...,9,A]
    }
}
```

**Output:**
```
Initial capacity: 10
Capacity after adding 10 elements: 10
Capacity after exceeding limit: 20
[0, 1, 2, ..., 9, A]
```

---

# **Stack in Java**

## **Introduction**
- `Stack` is a **child class** of `Vector`.
- It follows the **Last-In-First-Out (LIFO)** order.

---
## **Constructor**
| Constructor | Description |
|------------|-------------|
| `Stack s = new Stack();` | Creates an empty `Stack` object. |

---
## **Stack Methods**

| Method | Description |
|--------|-------------|
| `Object push(Object o)` | Inserts an object into the stack. |
| `Object pop()` | Removes and returns the top of the stack. |
| `Object peek()` | Returns the top of the stack without removal. |
| `boolean empty()` | Returns `true` if the stack is empty. |
| `int search(Object o)` | Returns the offset if the element is found, otherwise returns `-1`. |

---
## **Example: Stack in Action**
```java
import java.util.*;

public class StackDemo {
    public static void main(String[] args) {
        Stack<String> s = new Stack<>();
        s.push("A");
        s.push("B");
        s.push("C");
        
        System.out.println(s); // Output: [A, B, C]
        System.out.println("Search 'A': " + s.search("A")); // Output: 3
        System.out.println("Search 'Z': " + s.search("Z")); // Output: -1
    }
}
```

**Stack Representation:**
```
Index  |  Element  | Offset
----------------------------
2      |    C      | 1
1      |    B      | 2
0      |    A      | 3
----------------------------
```

---

# **Comparison of List Implementations in Java**

The `List` interface in Java is a part of the **Collection Framework** and represents an **ordered** collection of elements, allowing duplicates.

---
## **Comparison Table**

| Feature           | ArrayList | LinkedList | Vector | Stack |
|------------------|-----------|------------|--------|-------|
| **Underlying DS** | Resizable Array | Doubly Linked List | Resizable Array | Resizable Array |
| **Insertion Order** | ✅ Preserved | ✅ Preserved | ✅ Preserved | ✅ Preserved |
| **Duplicates Allowed** | ✅ Yes | ✅ Yes | ✅ Yes | ✅ Yes |
| **Null Allowed** | ✅ Yes | ✅ Yes | ✅ Yes | ✅ Yes |
| **Thread-Safe** | ❌ No | ❌ No | ✅ Yes (Synchronized) | ✅ Yes (Synchronized) |
| **Performance** | Fast retrieval, slow insert/delete | Fast insert/delete, slow retrieval | Slow due to synchronization | LIFO Operations |
| **Best For** | Frequent retrieval operations | Frequent insertions/deletions in the middle | Multi-threaded applications | LIFO (Undo, Backtracking) |
| **Implements RandomAccess** | ✅ Yes | ❌ No | ✅ Yes | ❌ No |

---
## **Key Takeaways**
- **`ArrayList`** is best for **retrieval operations**.
- **`LinkedList`** is best for **frequent insertions and deletions**.
- **`Vector`** is **synchronized**, making it thread-safe but slower.
- **`Stack`** follows **LIFO (Last-In-First-Out)** order, useful for **backtracking & undo operations**.

Choose the right `List` implementation based on performance needs and threading requirements! 🚀





