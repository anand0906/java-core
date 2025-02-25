# **Cursors in Java**

## **Introduction**

- If we want to **retrieve objects one by one** from a collection, we use **cursors**.
- Java provides **three types** of cursors:
  1. **Enumeration**
  2. **Iterator**
  3. **ListIterator**

---

## **1. Enumeration**

- Used to retrieve objects **one by one** from **legacy collection classes** like `Vector` and `Hashtable`.
- **Not a universal cursor** (only works with legacy classes).
- Provides **only read access** (no remove operation).

### **Creating an Enumeration Object**

We can create an `Enumeration` object using the `elements()` method of `Vector`.

```java
Vector v = new Vector();
Enumeration e = v.elements();
```

### **Methods in Enumeration**

| Method                      | Description                                  |
| --------------------------- | -------------------------------------------- |
| `boolean hasMoreElements()` | Returns `true` if more elements are present. |
| `Object nextElement()`      | Returns the next element.                    |

### **Example: Enumeration in Action**

```java
import java.util.*;

public class EnumerationDemo {
    public static void main(String[] args) {
        Vector<Integer> v1 = new Vector<>();
        for (int i = 0; i < 10; i++) {
            v1.addElement(i);
        }
        System.out.println(v1); // [0,1,2,3,...,9]

        Enumeration<Integer> e = v1.elements();
        while (e.hasMoreElements()) {
            Integer i = e.nextElement();
            if (i % 2 == 0) {
                System.out.println(i); // Prints even numbers: 0,2,4,6,8
            }
        }
    }
}
```

### **Limitations of Enumeration**

1. Works **only on legacy classes** (`Vector`, `Hashtable`).
2. **Cannot remove elements** while iterating.
3. To **overcome these limitations**, we use **Iterator**.

---

# **Iterator in Java**

## **Introduction**

- `Iterator` is a **universal cursor**, meaning it can be applied to any collection object.
- Allows **both read and remove operations** while iterating over a collection.
- We can create an `Iterator` object using the `iterator()` method of the `Collection` interface.

```java
public Iterator iterator();
```

### **Creating an Iterator**

```java
Iterator itr = c.iterator();
```

---

## **Methods in Iterator**

| Method              | Description                                         |
| ------------------- | --------------------------------------------------- |
| `boolean hasNext()` | Returns `true` if the collection has more elements. |
| `Object next()`     | Returns the next element in the iteration.          |
| `void remove()`     | Removes the last element returned by the iterator.  |

---

## **Example: Using Iterator**

```java
import java.util.*;

public class Main {
    public static void main(String[] args) {
        ArrayList<Integer> al = new ArrayList<>();
        for (int i = 0; i < 10; i++) {
            al.add(i);
        }
        System.out.println("Initial List: " + al);
        
        Iterator<Integer> itr = al.iterator();
        while (itr.hasNext()) {
            Integer i = itr.next();
            if (i % 2 == 0) {
                System.out.println(i); // Prints even numbers
            } else {
                itr.remove(); // Removes odd numbers
            }
        }
        System.out.println("Modified List: " + al); // [0,2,4,6,8]
    }
}
```

### **Output:**

```
Initial List: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
0
2
4
6
8
Modified List: [0, 2, 4, 6, 8]
```

---

## **Limitations of Iterator**

1. **Single Direction Cursor**: `Iterator` allows traversal only in the **forward direction** (no backward movement).
2. **Limited Operations**: Supports **only read and remove** operations; cannot add or replace elements.

### **Solution: ListIterator**

- To overcome these limitations, use `ListIterator`, which supports **bi-directional traversal** and allows element modification.

---

# **ListIterator in Java**

## **Introduction**

- `ListIterator` allows movement in **both forward and backward directions**.
- It enables **reading, removing, replacing, and adding elements**.
- `ListIterator` can be created using the `listIterator()` method of the `List` interface:

```java
public ListIterator listIterator();
```

### **Creating a ListIterator**

```java
ListIterator ltr = l.listIterator();
```

---

## **Relationship Between Iterator and ListIterator**

```java
Iterator(I) ----> ListIterator(I)
```

Since `ListIterator` extends `Iterator`, it inherits all methods of `Iterator`.

### **Methods in ListIterator**

| Method                  | Description                                                 |
| ----------------------- | ----------------------------------------------------------- |
| `boolean hasNext()`     | Checks if there is a next element (Forward direction).      |
| `Object next()`         | Returns the next element.                                   |
| `int nextIndex()`       | Returns the next element's index.                           |
| `boolean hasPrevious()` | Checks if there is a previous element (Backward direction). |
| `Object previous()`     | Returns the previous element.                               |
| `int previousIndex()`   | Returns the previous element's index.                       |
| `void remove()`         | Removes the last returned element.                          |
| `void add(Object o)`    | Adds a new element at the current position.                 |
| `void set(Object o)`    | Replaces the last element returned.                         |

---

## **Example: Using ListIterator**

```java
import java.util.*;

public class Main {
    public static void main(String[] args) {
        LinkedList<String> l = new LinkedList<>();
        l.add("balakrishna");
        l.add("venki");
        l.add("chiru");
        l.add("nag");
        System.out.println("Initial List: " + l);

        ListIterator<String> ltr = l.listIterator();
        while (ltr.hasNext()) {
            String s = ltr.next();
            if (s.equals("venki")) {
                ltr.remove(); // Removes "venki"
            } else if (s.equals("nag")) {
                ltr.add("chaintu"); // Adds "chaintu" after "nag"
            } else if (s.equals("chiru")) {
                ltr.set("charan"); // Replaces "chiru" with "charan"
            }
        }
        System.out.println("Modified List: " + l);
    }
}
```

### **Output:**

```
Initial List: [balakrishna, venki, chiru, nag]
Modified List: [balakrishna, charan, nag, chaintu]
```

---

## **Comparison Table: Enumeration vs Iterator vs ListIterator**

| Property               | Enumeration              | Iterator                     | ListIterator                    |
| ---------------------- | ------------------------ | ---------------------------- | ------------------------------- |
| **Applicable to**      | Only legacy classes      | Any collection object        | Only List objects               |
| **Legacy?**            | ✅ Yes                    | ❌ No                         | ❌ No                            |
| **Movement**           | Forward only             | Forward only                 | **Bidirectional**               |
| **Operations Allowed** | Read only                | Read & Remove                | **Read, Remove, Replace & Add** |
| **How to Obtain?**     | `elements()` of `Vector` | `iterator()` of `Collection` | `listIterator()` of `List`      |
| **Number of Methods**  | 2                        | 3                            | 9                               |

---

## **Internal Implementation of Cursors**

```java
import java.util.*;

public class Main {
    public static void main(String[] args) {
        Vector v = new Vector();
        Enumeration e = v.elements();
        Iterator itr = v.iterator();
        ListIterator litr = v.listIterator();

        System.out.println(e.getClass().getName());
        System.out.println(itr.getClass().getName());
        System.out.println(litr.getClass().getName());
    }
}
```

### **Output:**

```
java.util.Vector$1
java.util.Vector$Itr
java.util.Vector$ListItr
```

---

## **Key Takeaways**

- `ListIterator` is the **most powerful cursor** but is **only applicable for List objects**.
- Use `Iterator` for **general collections**.
- Use `Enumeration` for **legacy classes**.





