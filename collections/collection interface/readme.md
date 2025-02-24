# **Collection Interface in Java**

## **Introduction**

The `Collection` interface is the root of the Java Collection Framework and is used to represent a group of individual objects as a single entity. It provides the most common methods applicable to all collection types.

---

## **Key Features of Collection Interface**

- Allows storing multiple elements.
- Supports various utility methods for manipulation.
- It does not have any direct concrete implementation; instead, it is implemented by interfaces such as `List`, `Set`, and `Queue`.

---

## **Methods in Collection Interface**

Below are the commonly used methods in the `Collection` interface with explanations and examples.

### **1. Adding Elements**

- `boolean add(Object o)`: Adds a single object to the collection.
- `boolean addAll(Collection c)`: Adds all elements from another collection.

**Example:**

```java
import java.util.*;

public class CollectionExample {
    public static void main(String[] args) {
        Collection<String> fruits = new ArrayList<>();
        fruits.add("Apple");
        fruits.add("Banana");
        
        Collection<String> moreFruits = new ArrayList<>();
        moreFruits.add("Orange");
        moreFruits.add("Grapes");
        
        fruits.addAll(moreFruits);
        System.out.println(fruits); // Output: [Apple, Banana, Orange, Grapes]
    }
}
```

---

### **2. Removing Elements**

- `boolean remove(Object o)`: Removes a specific object.
- `boolean removeAll(Collection c)`: Removes all elements from another collection.
- `boolean retainAll(Collection c)`: Retains only the elements present in the specified collection.
- `void clear()`: Removes all elements from the collection.

**Example:**

```java
Collection<Integer> numbers = new ArrayList<>(Arrays.asList(1, 2, 3, 4, 5));
numbers.remove(3); // Removes element '3'
System.out.println(numbers); // Output: [1, 2, 4, 5]
```

---

### **3. Checking for Elements**

- `boolean contains(Object o)`: Checks if an element exists in the collection.
- `boolean containsAll(Collection c)`: Checks if all elements of another collection exist in the current collection.

**Example:**

```java
Collection<String> items = new HashSet<>();
items.add("Laptop");
items.add("Mouse");

System.out.println(items.contains("Laptop")); // Output: true
```

---

### **4. Utility Methods**

- `boolean isEmpty()`: Checks if the collection is empty.
- `int size()`: Returns the number of elements in the collection.
- `Object[] toArray()`: Converts the collection to an array.

**Example:**

```java
Collection<Double> prices = new ArrayList<>(Arrays.asList(10.5, 20.0, 30.75));
System.out.println("Size: " + prices.size()); // Output: Size: 3
System.out.println("Is Empty: " + prices.isEmpty()); // Output: Is Empty: false
```

---

### **5. Iterating Over Collection**

- `Iterator iterator()`: Returns an iterator to traverse the collection.

**Example:**

```java
Collection<String> names = new ArrayList<>(Arrays.asList("Alice", "Bob", "Charlie"));
Iterator<String> iterator = names.iterator();
while (iterator.hasNext()) {
    System.out.println(iterator.next());
}
```

---


