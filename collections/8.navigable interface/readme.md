# **Java Collection Framework: Enhancements in Java 1.6**

## **Introduction**
Java 1.6 introduced two key enhancements to the Collection Framework:
1. **NavigableSet (Interface)**
2. **NavigableMap (Interface)**

These interfaces extend `SortedSet` and `SortedMap`, respectively, adding advanced navigation methods.

---
# **NavigableSet (I)**
`NavigableSet` is a **child interface of `SortedSet`** and provides additional methods for navigation.

### **Hierarchy of NavigableSet**
```
Collection(I) (1.2)
    │
    ├── Set(I) (1.2)
        │
        ├── SortedSet(I) (1.2)
            │
            ├── NavigableSet(I) (1.6)
                │
                ├── TreeSet (1.2)
```

### **Methods in NavigableSet**
| Method | Description |
|--------|-------------|
| `floor(e)` | Returns the **highest element ≤ e**. |
| `lower(e)` | Returns the **highest element < e**. |
| `ceiling(e)` | Returns the **lowest element ≥ e**. |
| `higher(e)` | Returns the **lowest element > e**. |
| `pollFirst()` | Removes and returns the **first element**. |
| `pollLast()` | Removes and returns the **last element**. |
| `descendingSet()` | Returns a **reverse-order NavigableSet**. |

### **Example: Using NavigableSet**
```java
import java.util.*;

public class NavigableSetExample {
    public static void main(String[] args) {
        NavigableSet<Integer> t = new TreeSet<>();
        t.add(1000);
        t.add(2000);
        t.add(3000);
        t.add(4000);
        t.add(5000);
        
        System.out.println("Original Set: " + t);
        System.out.println("Ceiling of 2000: " + t.ceiling(2000)); // 2000
        System.out.println("Higher than 2000: " + t.higher(2000)); // 3000
        System.out.println("Floor of 3000: " + t.floor(3000)); // 3000
        System.out.println("Lower than 3000: " + t.lower(3000)); // 2000
        System.out.println("Poll First: " + t.pollFirst()); // 1000
        System.out.println("Poll Last: " + t.pollLast()); // 5000
        System.out.println("Descending Set: " + t.descendingSet()); // [4000, 3000, 2000]
        System.out.println("Final Set: " + t); // [2000, 3000, 4000]
    }
}
```

### **Output:**
```
Original Set: [1000, 2000, 3000, 4000, 5000]
Ceiling of 2000: 2000
Higher than 2000: 3000
Floor of 3000: 3000
Lower than 3000: 2000
Poll First: 1000
Poll Last: 5000
Descending Set: [4000, 3000, 2000]
Final Set: [2000, 3000, 4000]
```

---
# **NavigableMap (I)**
`NavigableMap` is a **child interface of `SortedMap`** and provides navigation methods similar to `NavigableSet`.

### **Hierarchy of NavigableMap**
```
Map(I)
    │
    ├── SortedMap(I) (1.2)
        │
        ├── NavigableMap(I) (1.6)
            │
            ├── TreeMap (1.2)
```

### **Methods in NavigableMap**
| Method | Description |
|--------|-------------|
| `floorKey(e)` | Returns the **highest key ≤ e**. |
| `lowerKey(e)` | Returns the **highest key < e**. |
| `ceilingKey(e)` | Returns the **lowest key ≥ e**. |
| `higherKey(e)` | Returns the **lowest key > e**. |
| `pollFirstEntry()` | Removes and returns the **first key-value pair**. |
| `pollLastEntry()` | Removes and returns the **last key-value pair**. |
| `descendingMap()` | Returns a **reverse-order NavigableMap**. |

### **Example: Using NavigableMap**
```java
import java.util.*;

public class NavigableMapExample {
    public static void main(String[] args) {
        TreeMap<String, String> tr = new TreeMap<>();
        tr.put("B", "Banana");
        tr.put("C", "Cat");
        tr.put("A", "Apple");
        tr.put("D", "Dog");
        tr.put("G", "Gun");
        
        System.out.println("Original Map: " + tr);
        System.out.println("Ceiling Key of 'C': " + tr.ceilingKey("C")); // C
        System.out.println("Higher Key than 'E': " + tr.higherKey("E")); // G
        System.out.println("Floor Key of 'E': " + tr.floorKey("E")); // D
        System.out.println("Lower Key than 'E': " + tr.lowerKey("E")); // D
        System.out.println("Poll First Entry: " + tr.pollFirstEntry()); // A=Apple
        System.out.println("Poll Last Entry: " + tr.pollLastEntry()); // G=Gun
        System.out.println("Descending Map: " + tr.descendingMap()); // {D=Dog, C=Cat, B=Banana}
        System.out.println("Final Map: " + tr); // {B=Banana, C=Cat, D=Dog}
    }
}
```

### **Output:**
```
Original Map: {A=Apple, B=Banana, C=Cat, D=Dog, G=Gun}
Ceiling Key of 'C': C
Higher Key than 'E': G
Floor Key of 'E': D
Lower Key than 'E': D
Poll First Entry: A=Apple
Poll Last Entry: G=Gun
Descending Map: {D=Dog, C=Cat, B=Banana}
Final Map: {B=Banana, C=Cat, D=Dog}
```

---
# **Key Takeaways**
✅ **`NavigableSet` provides advanced navigation methods for sets**.
✅ **`NavigableMap` extends `SortedMap` with key-based navigation features**.
✅ **Both interfaces improve efficiency in ordered collections**.
✅ **TreeSet and TreeMap implement these interfaces**.

This guide provides a structured explanation of **NavigableSet and NavigableMap**, with examples and best practices. 🚀

