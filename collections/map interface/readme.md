# **Map in Java**

## **Introduction**
- `Map` is **not** a child interface of `Collection`.
- Used to represent a **group of key-value pairs**.
- Keys are **unique**, but values can be **duplicated**.
- Each key-value pair is called an **entry**.

### **Hierarchy of Map Interface**
```
                  Map(I) (1.2V)
                     |
     ------------------------------------
     |           |           |         |
  HashMap     WeakHashMap  IdentityHashMap  SortedMap(I) (1.2V)
     |                                       |
  LinkedHashMap (1.4V)                  NavigableMap(I) (1.6V)
                                             |
                                          TreeMap (1.2V)
```

---
## **Example of Key-Value Pairs in Map**
| Key  | Value  |
|------|--------|
| 101  | Durga  |
| 102  | Ravi   |
| 103  | Shiva  |
| 104  | Pawan  |

### **Key Properties of Map**
1. **Keys and values are objects**.
2. **Duplicate keys are not allowed**, but values can be duplicated.
3. `Map` is considered a **collection of entry objects**.

---
## **Methods in Map Interface**

| Method | Description |
|--------|-------------|
| `Object put(Object key, Object value)` | Adds a key-value pair to the map. If the key exists, replaces the old value and returns it. |
| `void putAll(Map m)` | Adds all key-value pairs from another map. |
| `Object get(Object key)` | Returns the value associated with the given key. |
| `Object remove(Object key)` | Removes the entry for the given key. |
| `boolean containsKey(Object key)` | Checks if the key exists in the map. |
| `boolean containsValue(Object value)` | Checks if the value exists in the map. |
| `boolean isEmpty()` | Checks if the map is empty. |
| `int size()` | Returns the number of key-value pairs in the map. |
| `void clear()` | Removes all key-value pairs from the map. |
| `Set keySet()` | Returns a `Set` of all keys in the map. |
| `Collection values()` | Returns a collection of all values in the map. |
| `Set entrySet()` | Returns a `Set` view of all key-value pairs. |

---
## **Example: Using Map Methods**
```java
import java.util.*;

public class MapExample {
    public static void main(String[] args) {
        Map<Integer, String> map = new HashMap<>();
        
        map.put(101, "Durga");
        map.put(102, "Shiva");
        map.put(103, "Ravi");
        
        System.out.println("Map: " + map);
        System.out.println("Value for key 101: " + map.get(101));
        System.out.println("Contains key 102? " + map.containsKey(102));
        System.out.println("Contains value 'Ravi'? " + map.containsValue("Ravi"));
        map.remove(103);
        System.out.println("After removing key 103: " + map);
    }
}
```

---
# **Entry Interface in Java Map**

## **Introduction**
- A `Map` stores **key-value pairs**, and each **key-value pair is called an Entry**.
- `Entry` objects exist **only within a Map**, and cannot exist independently.
- The `Entry` interface is defined inside the `Map` interface.

### **Entry Interface Definition**
```java
interface Map {
    interface Entry {
        Object getKey();
        Object getValue();
        Object setValue(Object obj);
    }
}
```

These methods apply **only** to `Entry` objects.

---
## **HashMap in Java**

### **Key Features**
1. The underlying **data structure** is a **HashTable**.
2. **Insertion order is not preserved**; elements are arranged based on the **hash code of keys**.
3. **Duplicate keys are not allowed**, but values **can be duplicated**.
4. **Heterogeneous objects** are allowed as both **keys and values**.
5. **Null is allowed** for keys (**only once**), but values **can have multiple nulls**.
6. Implements `Serializable` and `Cloneable`, but **not** `RandomAccess`.
7. **Best choice** when **frequent search operations** are needed.

### **Constructors in HashMap**

| Constructor | Description |
|------------|-------------|
| `HashMap()` | Default capacity **16**, default load factor **0.75**. |
| `HashMap(int initialCapacity)` | Custom capacity with **default load factor (0.75)**. |
| `HashMap(int initialCapacity, float loadFactor)` | Custom capacity and **custom load factor**. |
| `HashMap(Map m)` | Creates a new `HashMap` with entries from another map. |

---
## **Example: HashMap Operations**
```java
import java.util.*;

public class MapTest {
    public static void main(String[] args) {
        HashMap<String, Integer> m = new HashMap<>();
        m.put("chiranjeevi", 700);
        m.put("balaiah", 800);
        m.put("venkatesh", 200);
        m.put("nagarjuna", 500);
        System.out.println(m);
        
        System.out.println(m.put("chiranjeevi", 1000)); // Overwrites value
        System.out.println("Keys: " + m.keySet());
        System.out.println("Values: " + m.values());
        System.out.println("Entries: " + m.entrySet());
        
        // Iterating over entries
        for (Map.Entry<String, Integer> entry : m.entrySet()) {
            System.out.println(entry.getKey() + "------" + entry.getValue());
            if (entry.getKey().equals("nagarjuna")) {
                entry.setValue(10000); // Updating value
            }
        }
        System.out.println("Updated Map: " + m);
    }
}
```

### **Output:**
```
{chiranjeevi=700, balaiah=800, venkatesh=200, nagarjuna=500}
700
Keys: [chiranjeevi, balaiah, venkatesh, nagarjuna]
Values: [1000, 800, 200, 500]
Entries: [chiranjeevi=1000, balaiah=800, venkatesh=200, nagarjuna=500]
chiranjeevi------1000
balaiah------800
venkatesh------200
nagarjuna------500
Updated Map: {chiranjeevi=1000, balaiah=800, venkatesh=200, nagarjuna=10000}
```

---
## **HashMap vs HashTable**

| Feature | HashMap | HashTable |
|---------|--------|-----------|
| **Thread-Safety** | ❌ Not synchronized | ✅ Synchronized (Thread-safe) |
| **Performance** | 🚀 Faster | 🐢 Slower (because of synchronization) |
| **Null Keys Allowed?** | ✅ Yes (Only one) | ❌ No (throws `NullPointerException`) |
| **Null Values Allowed?** | ✅ Yes (Multiple) | ❌ No |
| **Introduced In** | Java 1.2 (Not legacy) | Java 1.0 (Legacy) |

---
## **How to Get a Synchronized Version of HashMap?**
By default, `HashMap` is **not synchronized**, but we can create a **thread-safe version** using `Collections.synchronizedMap()`.

```java
HashMap<String, Integer> map = new HashMap<>();
Map<String, Integer> syncMap = Collections.synchronizedMap(map);
```

# **LinkedHashMap in Java**

## **Introduction**
- `LinkedHashMap` is a **child class of HashMap**.
- It is almost identical to `HashMap` in terms of **methods and constructors** but has key differences.

---
## **Differences Between HashMap and LinkedHashMap**

| Feature | HashMap | LinkedHashMap |
|---------|--------|--------------|
| **Underlying Data Structure** | HashTable | **Combination of LinkedList and HashTable** (Hybrid Structure) |
| **Insertion Order** | ❌ Not preserved (based on key's hash code) | ✅ Preserved |
| **Introduced In** | Java 1.2 | Java 1.4 |

### **Example: Preserved Insertion Order in LinkedHashMap**
If we replace `HashMap` with `LinkedHashMap` in a program, the output will maintain insertion order:
```java
import java.util.*;

public class LinkedHashMapExample {
    public static void main(String[] args) {
        LinkedHashMap<String, Integer> m = new LinkedHashMap<>();
        m.put("chiranjeevi", 700);
        m.put("balaiah", 800);
        m.put("venkatesh", 200);
        m.put("nagarjuna", 500);
        System.out.println(m); 
    }
}
```

### **Output:**
```
{chiranjeevi=700, balaiah=800, venkatesh=200, nagarjuna=500}
```

💡 **Key Takeaway:** Unlike `HashMap`, `LinkedHashMap` **preserves insertion order**, making it useful for **cache-based applications**.

---
# **Difference Between `==` Operator and `.equals()` Method**

| Feature | `==` Operator | `.equals()` Method |
|---------|--------------|-------------------|
| **Type of Comparison** | **Reference Comparison** (checks memory addresses) | **Content Comparison** (checks actual values) |
| **Applicable To** | **Primitive types and object references** | **Mostly for objects (can be overridden)** |
| **Usage Example** | `i1 == i2` | `i1.equals(i2)` |

### **Example:**
```java
public class EqualsVsDoubleEquals {
    public static void main(String[] args) {
        Integer i1 = new Integer(10);
        Integer i2 = new Integer(10);
        
        System.out.println(i1 == i2); // False (Different memory addresses)
        System.out.println(i1.equals(i2)); // True (Same content)
    }
}
```

### **Output:**
```
false
true
```
---

# **IdentityHashMap**

- `IdentityHashMap` is similar to `HashMap`  but it uses `==` **(reference comparison)** instead of `.equals()` **(content comparison)** to check for duplicate keys.

### **Key Differences Between HashMap and IdentityHashMap**

| Feature            | HashMap                                              | IdentityHashMap                                             |
| ------------------ | ---------------------------------------------------- | ----------------------------------------------------------- |
| **Key Comparison** | Uses `.equals()` (content comparison)                | Uses `==` (reference comparison)                            |
| **Duplicates**     | Keys with the same content are considered duplicates | Only keys with the same reference are considered duplicates |
| **Use Case**       | General-purpose key-value storage                    | Cases where reference equality is required                  |

### **Example: IdentityHashMap vs HashMap**

```java
import java.util.*;

public class IdentityHashMapExample {
    public static void main(String[] args) {
        HashMap<Integer, String> hashMap = new HashMap<>();
        Integer i1 = new Integer(10);
        Integer i2 = new Integer(10);
        hashMap.put(i1, "Pawan");
        hashMap.put(i2, "Kalyan");
        System.out.println("HashMap: " + hashMap); // {10=Kalyan} (i1 and i2 are treated as the same key)
        
        IdentityHashMap<Integer, String> identityMap = new IdentityHashMap<>();
        identityMap.put(i1, "Pawan");
        identityMap.put(i2, "Kalyan");
        System.out.println("IdentityHashMap: " + identityMap); // {10=Pawan, 10=Kalyan} (i1 and i2 are treated as separate keys)
    }
}
```

### **Output:**

```
HashMap: {10=Kalyan}
IdentityHashMap: {10=Pawan, 10=Kalyan}
```

💡 **Key Takeaway:** Use `IdentityHashMap` when **reference equality (**`==`**)** is required instead of **content equality (********`.equals()`********\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*)**.

---

# **WeakHashMap in Java**

## **Introduction**

- `WeakHashMap` is similar to **`HashMap`**, but it allows its keys to be garbage collected when no longer referenced elsewhere.
- **Garbage Collector (GC) dominates WeakHashMap**, whereas **HashMap dominates GC**.

### **Key Differences Between HashMap and WeakHashMap**

| Feature                | HashMap                                                   | WeakHashMap                                                          |
| ---------------------- | --------------------------------------------------------- | -------------------------------------------------------------------- |
| **Garbage Collection** | Keys are **not** eligible for GC if referenced in the map | Keys are **eligible** for GC when no external references exist       |
| **Memory Usage**       | Can cause **memory leaks** due to strong references       | Avoids **memory leaks** by allowing key removal                      |
| **Use Case**           | Caching where strong key references are needed            | Caching where keys should be removed automatically when unreferenced |

### **Example: WeakHashMap vs HashMap**

```java
import java.util.*;

public class WeakHashMapExample {
    public static void main(String[] args) throws InterruptedException {
        Map<Temp, String> map = new WeakHashMap<>();
        Temp t = new Temp();
        map.put(t, "Durga");
        System.out.println("Before GC: " + map);
        t = null;
        System.gc();
        Thread.sleep(5000);
        System.out.println("After GC: " + map);
    }
}

class Temp {
    public String toString() {
        return "temp";
    }
    @Override
    protected void finalize() throws Throwable {
        System.out.println("Finalize method called");
    }
}
```

### **Output:**

```
Before GC: {temp=Durga}
Finalize method called
After GC: {}
```

💡 **Key Takeaway:** Use `WeakHashMap` when keys should be **automatically removed** when they **are no longer strongly referenced elsewhere**.

---

## **Final Thoughts**

- \*\*Use \*\***`IdentityHashMap`** when **you need reference equality (`==`) instead of content equality (`.equals()`)**.
- \*\*Use \*\***`WeakHashMap`** when **keys should be garbage collected if no longer referenced elsewhere**.

# **SortedMap in Java**

## **Introduction**
- `SortedMap` is a **child interface of `Map`**.
- It maintains **key-value pairs in a sorted order based on keys**.
- Sorting is always **based on keys**, not values.

---
## **Methods in SortedMap**
| Method | Description |
|--------|-------------|
| `Object firstKey()` | Returns the first (lowest) key. |
| `Object lastKey()` | Returns the last (highest) key. |
| `SortedMap headMap(Object key)` | Returns entries **less than** the given key. |
| `SortedMap tailMap(Object key)` | Returns entries **greater than or equal to** the given key. |
| `SortedMap subMap(Object key1, Object key2)` | Returns entries **between key1 (inclusive) and key2 (exclusive)**. |
| `Comparator comparator()` | Returns the **Comparator** used for sorting, or `null` if using natural ordering. |

### **Example: SortedMap in Action**
```java
import java.util.*;

public class SortedMapExample {
    public static void main(String[] args) {
        SortedMap<Integer, String> sm = new TreeMap<>();
        sm.put(101, "A");
        sm.put(103, "B");
        sm.put(104, "C");
        sm.put(107, "D");
        sm.put(125, "E");
        sm.put(136, "F");

        System.out.println("First Key: " + sm.firstKey()); // 101
        System.out.println("Last Key: " + sm.lastKey()); // 136
        System.out.println("Head Map (Up to 104): " + sm.headMap(104)); // {101=A, 103=B}
        System.out.println("Tail Map (From 107): " + sm.tailMap(107)); // {107=D, 125=E, 136=F}
        System.out.println("Sub Map (103 to 125): " + sm.subMap(103, 125)); // {103=B, 104=C, 107=D}
    }
}
```

---
# **TreeMap in Java**

## **Introduction**
- The **underlying data structure** is a **Red-Black Tree**.
- **Insertion order is not preserved**, sorting is based on **keys**.
- **Duplicate keys are not allowed**, but **duplicate values are allowed**.
- **Keys must be comparable** (either using **natural sorting** or a **custom comparator**).

---
## **Null Acceptance in TreeMap**
| Condition | Behavior |
|-----------|----------|
| Empty `TreeMap`, first key as `null` (Java 1.6 and earlier) | ✅ Allowed |
| Non-empty `TreeMap`, inserting a `null` key | ❌ `NullPointerException` |
| `TreeMap` in Java 1.7+ | ❌ `NullPointerException` for null keys |
| Null values | ✅ Allowed (no restrictions) |

---
## **TreeMap Constructors**
| Constructor | Description |
|------------|-------------|
| `TreeMap()` | Uses **natural sorting** for keys. |
| `TreeMap(Comparator c)` | Uses **custom sorting** defined by `Comparator`. |
| `TreeMap(SortedMap m)` | Creates a `TreeMap` from a `SortedMap`. |
| `TreeMap(Map m)` | Creates a `TreeMap` from any `Map`. |

---
## **Example: Default Natural Sorting Order in TreeMap**
```java
import java.util.*;

public class TreeMapDemo {
    public static void main(String[] args) {
        TreeMap<Integer, String> m = new TreeMap<>();
        m.put(100, "ZZZ");
        m.put(103, "YYY");
        m.put(101, "XXX");
        m.put(104, "106");

        // m.put("FFFF", "XXX"); // Causes ClassCastException
        // m.put(null, "XXX"); // Causes NullPointerException in Java 1.7+

        System.out.println(m);
        // Output: {100=ZZZ, 101=XXX, 103=YYY, 104=106}
    }
}
```

---
## **Example: Custom Sorting Using a Comparator**
```java
import java.util.*;

public class CustomTreeMapSorting {
    public static void main(String[] args) {
        TreeMap<String, Integer> m = new TreeMap<>(new MyComparator());
        m.put("XXX", 10);
        m.put("AAA", 20);
        m.put("ZZZ", 30);
        m.put("LLL", 40);

        System.out.println(m);
        // Output: {ZZZ=30, XXX=10, LLL=40, AAA=20} (Sorted in descending order)
    }
}

class MyComparator implements Comparator<String> {
    @Override
    public int compare(String s1, String s2) {
        return s2.compareTo(s1); // Reverse order
    }
}
```

### **Key Takeaways**
- `SortedMap` is useful when **sorting based on keys is required**.
- `TreeMap` provides **automatic key sorting** and supports **custom comparators**.
- `Null keys` are **not allowed from Java 1.7 onwards**, but **null values are allowed**.











