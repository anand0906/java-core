------------------------
Utility Classes in Java
------------------------

## **Collections Class**
`Collections` class provides utility methods for collection objects like sorting, searching, reversing, etc.

### **Sorting Elements of List**
Collections class defines two sorting methods:

1. **`public static void sort(List l)`**
   - Sorts a list based on **Default Natural Sorting Order**.
   - List must contain **homogeneous and comparable** objects; otherwise, a `ClassCastException` occurs.
   - If the list contains `null`, a `NullPointerException` is thrown.

2. **`public static void sort(List l, Comparator c)`**
   - Sorts based on a **Custom Sorting Order** defined by a `Comparator`.

#### **Example: Default Sorting Order**
```java
import java.util.*;
public class CollectionsSortExample {
    public static void main(String[] args) {
        ArrayList<String> list = new ArrayList<>();
        list.add("Z"); list.add("A"); list.add("K"); list.add("N");
        System.out.println("Before Sorting: " + list);
        Collections.sort(list);
        System.out.println("After Sorting: " + list);
    }
}
```
**Output:**
```
Before Sorting: [Z, A, K, N]
After Sorting: [A, K, N, Z]
```

#### **Example: Custom Sorting Order**
```java
import java.util.*;
class MyComparator implements Comparator<String> {
    public int compare(String s1, String s2) {
        return s2.compareTo(s1); // Reverse order
    }
}
public class CustomSortingExample {
    public static void main(String[] args) {
        ArrayList<String> list = new ArrayList<>();
        list.add("Z"); list.add("A"); list.add("K"); list.add("N");
        System.out.println("Before Sorting: " + list);
        Collections.sort(list, new MyComparator());
        System.out.println("After Sorting: " + list);
    }
}
```
**Output:**
```
Before Sorting: [Z, A, K, N]
After Sorting: [Z, N, K, A]
```

---
## **Searching Elements in a List**
`Collections` class provides binary search methods:
1. **`binarySearch(List l, Object target)`** → Default Sorting Order.
2. **`binarySearch(List l, Object target, Comparator c)`** → Custom Sorting Order.

### **Example: Binary Search**
```java
import java.util.*;
public class CollectionsSearchExample {
    public static void main(String[] args) {
        ArrayList<String> list = new ArrayList<>();
        list.add("Z"); list.add("A"); list.add("M"); list.add("K");
        Collections.sort(list);
        System.out.println("Sorted List: " + list);
        System.out.println("Index of 'Z': " + Collections.binarySearch(list, "Z"));
        System.out.println("Index of 'J': " + Collections.binarySearch(list, "J"));
    }
}
```
**Output:**
```
Sorted List: [A, K, M, Z]
Index of 'Z': 3
Index of 'J': -2
```

---
## **Reversing Elements of a List**
```java
Collections.reverse(List l);
```
### **Example: Reversing List**
```java
import java.util.*;
public class ReverseExample {
    public static void main(String[] args) {
        ArrayList<Integer> list = new ArrayList<>(Arrays.asList(15, 0, 20, 10, 5));
        System.out.println("Before Reverse: " + list);
        Collections.reverse(list);
        System.out.println("After Reverse: " + list);
    }
}
```
**Output:**
```
Before Reverse: [15, 0, 20, 10, 5]
After Reverse: [5, 10, 20, 0, 15]
```

---
## **Arrays Utility Class**
`Arrays` class provides utility methods for array manipulation, including sorting, searching, and conversion to lists.

### **Sorting Arrays**
```java
Arrays.sort(array);
Arrays.sort(array, Comparator c);
```
### **Example: Sorting Arrays**
```java
import java.util.*;
public class ArraysSortingExample {
    public static void main(String[] args) {
        int[] arr = {10, 5, 20, 11, 6};
        Arrays.sort(arr);
        System.out.println("Sorted Array: " + Arrays.toString(arr));
    }
}
```
**Output:**
```
Sorted Array: [5, 6, 10, 11, 20]
```

---
## **Binary Search in Arrays**
```java
Arrays.binarySearch(array, target);
```
### **Example: Searching Arrays**
```java
import java.util.*;
public class ArraysSearchExample {
    public static void main(String[] args) {
        int[] arr = {10, 5, 20, 11, 6};
        Arrays.sort(arr);
        System.out.println("Index of 6: " + Arrays.binarySearch(arr, 6));
    }
}
```
**Output:**
```
Index of 6: 1
```

---
## **Converting Array to List**
```java
List<String> list = Arrays.asList(array);
```
### **Example: Array to List Conversion**
```java
import java.util.*;
public class ArrayToListExample {
    public static void main(String[] args) {
        String[] arr = {"A", "Z", "B"};
        List<String> list = Arrays.asList(arr);
        System.out.println("List: " + list);
    }
}
```
**Output:**
```
List: [A, Z, B]
```
---
## **Key Takeaways**
✅ `Collections` class provides sorting, searching, reversing for collections.
✅ `Arrays` class provides sorting, searching, and conversion for arrays.
✅ **Binary search** requires the array/list to be sorted first.
✅ **Reverse** method is used for reversing a list.
✅ **Arrays.asList()** converts an array to a list, but it is **not resizable**.

This structured guide provides an overview of Java's `Collections` and `Arrays` utility classes with examples and best practices. 🚀

