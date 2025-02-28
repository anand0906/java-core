# Streams
Streams is used to process objects of the collection, in Java SE 8, the Streams concept was introduced.
A stream in Java is a sequence of objects that supports various methods that can be pipelined to produce the desired result. 

**Difference between Collection and Stream**
- If we want to represent a group of individual objects as a single entity, then we should go for a **collection**.
- If we want to **process** a group of objects from the collection, then we should go for **streams**.
- We can create a stream object from a collection by using the `stream()` method of the Collection interface. This method is a **default method** added to the Collection in Java SE 8.

```java
default Stream<T> stream()
```
Example:
```java
Stream<Integer> s = list.stream();
```

Stream is an interface present in **java.util.stream**. Once we obtain the stream, we can process objects of that collection in two phases:
1. **Configuration**
2. **Processing**

## 1) Configuration:
We can configure a stream either by using a **filter** or by using a **map** mechanism.

### **Filtering:**
- We can filter elements from the collection based on some boolean condition using the `filter()` method of the Stream interface.
- This method takes a **Predicate<T>** function as an argument.

```java
public Stream<T> filter(Predicate<T> predicate)
```
Example:
```java
Stream<Integer> s = list.stream();
Stream<Integer> s1 = s.filter(i -> i % 2 == 0);
```

### **Mapping:**
- If we want to create a new object for every object present in the collection, based on our requirement, we use the `map()` method of the Stream interface.

```java
public Stream<R> map(Function<T, R> function)
```
Example:
```java
Stream<Integer> s = list.stream();
Stream<Integer> s1 = s.map(i -> i + 10);
```

After configuration, we can process objects using various methods.

## 2) Processing Methods:
### **Processing by `collect()` method**
- This method collects elements from the stream and adds them to a specified collection.

#### Example 1: Collecting even numbers from a list
##### **Without Streams:**
```java
import java.util.*;
class Test {
    public static void main(String[] args) {
        ArrayList<Integer> list = new ArrayList<>();
        for (int i = 0; i <= 10; i++) {
            list.add(i);
        }
        System.out.println(list);
        ArrayList<Integer> evenList = new ArrayList<>();
        for (Integer i : list) {
            if (i % 2 == 0)
                evenList.add(i);
        }
        System.out.println(evenList);
    }
}
```

##### **With Streams:**
```java
import java.util.*;
import java.util.stream.*;
class Test {
    public static void main(String[] args) {
        ArrayList<Integer> list = new ArrayList<>();
        for (int i = 0; i <= 10; i++) {
            list.add(i);
        }
        System.out.println(list);
        List<Integer> evenList = list.stream().filter(i -> i % 2 == 0).collect(Collectors.toList());
        System.out.println(evenList);
    }
}
```

### **Processing by `count()` method**
- This method returns the number of elements present in the stream.

```java
long count = list.stream().filter(s -> s.length() == 5).count();
System.out.println("Number of 5-letter words: " + count);
```

### **Processing by `sorted()` method**
- The sorting can either be **default natural sorting order** or **customized sorting order** specified by a comparator.
```java
sorted() // default natural sorting order
sorted(Comparator c) // customized sorting order
```
Example:
```java
List<String> sortedList = list.stream().sorted().collect(Collectors.toList());
System.out.println("Default sorting order: " + sortedList);

List<String> customSortedList = list.stream()
    .sorted((s1, s2) -> -s1.compareTo(s2))
    .collect(Collectors.toList());
System.out.println("Custom sorting order: " + customSortedList);
```

### **Processing by `min()` and `max()` methods**
```java
String min = list.stream().min((s1, s2) -> s1.compareTo(s2)).get();
System.out.println("Minimum value: " + min);

String max = list.stream().max((s1, s2) -> s1.compareTo(s2)).get();
System.out.println("Maximum value: " + max);
```

### **Processing by `forEach()` method**
- This method applies a lambda expression to each element of the stream.

```java
list.stream().forEach(s -> System.out.println(s));
list.stream().forEach(System.out::println);
```

Example:
```java
import java.util.*;
import java.util.stream.*;
class Test {
    public static void main(String[] args) {
        ArrayList<Integer> list = new ArrayList<>();
        list.add(10); list.add(20); list.add(30); list.add(40);
        list.stream().forEach(System.out::println);
    }
}
```

### **Processing by `toArray()` method**
- We can use `toArray()` to copy elements of the stream into an array.

```java
Integer[] arr = list.stream().toArray(Integer[]::new);
for (Integer i : arr) {
    System.out.println(i);
}
```

### **Processing by `Stream.of()` method**
- We can create a stream from a group of values or an array.

```java
Stream<Integer> s = Stream.of(99, 999, 9999, 99999);
s.forEach(System.out::println);

Double[] d = {10.0, 10.1, 10.2, 10.3};
Stream<Double> s1 = Stream.of(d);
s1.forEach(System.out::println);
```

---

With this, **Streams** enable efficient and declarative processing of collections with operations like filtering, mapping, sorting, and reducing, making the code more readable and concise.

## Processing Objects by Using flatMap() Method

Both **map()** and **flatMap()** can be applied to a `Stream<T>` and both return a `Stream<R>`. The difference between them is:
- The `map()` operation produces **one output value** for each input value.
- The `flatMap()` operation produces an **arbitrary number** (zero or more) values for each input value.

A typical use case is when the mapper function in `flatMap()` returns:
- `Stream.empty()` if it wants to send **zero** values.
- `Stream.of(x, y, z)` if it wants to return **multiple** values.

## Demo Program

```java
import java.util.*;
import java.util.stream.*;

public class Test {
    public static void main(String[] args) {
        ArrayList<Integer> l1 = new ArrayList<Integer>();
        for (int i = 0; i <= 10; i++) {
            l1.add(i);
        }
        System.out.println("Before using map() method: " + l1);

        // Using flatMap to filter even numbers
        List<Integer> l2 = l1.stream().flatMap(
            i -> { 
                if (i % 2 != 0) return Stream.empty(); 
                else return Stream.of(i); 
            }
        ).collect(Collectors.toList());
        System.out.println("After using flatMap() method: " + l2);

        // Using flatMap to store both number and its square if it is even
        List<Integer> l3 = l1.stream().flatMap(
            i -> {
                if (i % 2 != 0) return Stream.empty(); 
                else return Stream.of(i, i * i); 
            }
        ).collect(Collectors.toList());
        System.out.println("After using flatMap() method with squares: " + l3);
    }
}
```

### Output
```
Before using map() method: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
After using flatMap() method: [0, 2, 4, 6, 8, 10]
After using flatMap() method with squares: [0, 0, 2, 4, 4, 16, 6, 36, 8, 64, 10, 100]
```

## Difference Between `map()` and `flatMap()`
| Feature         | `map()`  | `flatMap()`  |
|---------------|---------|------------|
| Input-to-Output Mapping | One-to-One | One-to-Many |
| Output Type | `Stream<T>` | `Stream<R>` |
| Use Case | Transform each element | Flatten and transform collections |
| Example | Convert list of strings to uppercase | Flatten list of lists into a single list |

### Example of `map()`
```java
List<String> words = Arrays.asList("Hello", "World");
List<String> mapped = words.stream().map(String::toUpperCase).collect(Collectors.toList());
System.out.println(mapped); // [HELLO, WORLD]
```

### Example of `flatMap()`
```java
List<List<String>> nestedList = Arrays.asList(
    Arrays.asList("Hello", "World"),
    Arrays.asList("Java", "Streams")
);
List<String> flatMapped = nestedList.stream()
    .flatMap(Collection::stream)
    .collect(Collectors.toList());
System.out.println(flatMapped); // [Hello, World, Java, Streams]
```

### Conclusion
- **`map()`** is used for transforming **each element** of a collection.
- **`flatMap()`** is used when you need to **flatten nested structures** before processing them further.

## Java 9 Enhancements for Stream API 

Java 9 introduced several enhancements to the **Stream API**, adding powerful methods for better control over stream operations. The following new methods were introduced:

1. **takeWhile()**  
2. **dropWhile()**  
3. **Stream.iterate()**  
4. **Stream.ofNullable()**  

> **Note:**  
> - `takeWhile()` and `dropWhile()` are **default methods** of the `Stream` interface.  
> - `iterate()` and `ofNullable()` are **static methods** of the `Stream` interface.  

---

## **1. takeWhile() Method**  

### **Definition:**  
- The `takeWhile()` method returns a **stream of elements** that satisfy the given predicate.  
- It is **similar** to the `filter()` method but with a **key difference**:  
  - `filter()` processes **all** elements and selects those that match the predicate.  
  - `takeWhile()` processes elements **until** the predicate **fails**. Once it fails, the rest of the elements are discarded.  

### **Syntax:**  
```java
default Stream<T> takeWhile(Predicate<? super T> predicate)
```

### **Example:**  
```java
import java.util.*;
import java.util.stream.*;

public class TakeWhileExample {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(2, 4, 6, 3, 7, 8, 10);
        
        List<Integer> result = numbers.stream()
                                    .takeWhile(n -> n % 2 == 0) // Stops at the first odd number
                                    .collect(Collectors.toList());
        
        System.out.println("After takeWhile: " + result);
    }
}
```
**Output:**  
```
After takeWhile: [2, 4, 6]
```

---

## **2. dropWhile() Method**  

### **Definition:**  
- The `dropWhile()` method **removes** elements **until** the predicate **fails** and processes the rest.
- It is the **opposite** of `takeWhile()`. 

### **Syntax:**  
```java
default Stream<T> dropWhile(Predicate<? super T> predicate)
```

### **Example:**  
```java
import java.util.*;
import java.util.stream.*;

public class DropWhileExample {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(2, 4, 6, 3, 7, 8, 10);
        
        List<Integer> result = numbers.stream()
                                    .dropWhile(n -> n % 2 == 0) // Drops even numbers at the beginning
                                    .collect(Collectors.toList());
        
        System.out.println("After dropWhile: " + result);
    }
}
```
**Output:**  
```
After dropWhile: [3, 7, 8, 10]
```

---

## **3. Stream.iterate() Method**  

### **Definition:**  
- The `iterate()` method in Java 9 prevents infinite loops by introducing a termination condition.  
- It is similar to a **for-loop**.  

### **Syntax:**  
```java
public static <T> Stream<T> iterate(T seed, Predicate<? super T> hasNext, UnaryOperator<T> next)
```

### **Example:**  
```java
import java.util.stream.*;

public class IterateExample {
    public static void main(String[] args) {
        Stream.iterate(1, x -> x < 10, x -> x + 2)
              .forEach(System.out::println);
    }
}
```
**Output:**  
```
1
3
5
7
9
```

---

## **4. Stream.ofNullable() Method**  

### **Definition:**  
- The `ofNullable()` method helps prevent **NullPointerException** by returning an **empty stream** if the provided element is `null`.  

### **Syntax:**  
```java
public static <T> Stream<T> ofNullable(T value)
```

### **Example:**  
```java
import java.util.*;
import java.util.stream.*;

public class OfNullableExample {
    public static void main(String[] args) {
        Stream.ofNullable("Hello").forEach(System.out::println);
        Stream.ofNullable(null).forEach(System.out::println); // Won't print anything
    }
}
```
**Output:**  
```
Hello
```

---

## **Conclusion**  
Java 9 **Stream API enhancements** provide better control over stream processing with **takeWhile(), dropWhile(), iterate(), and ofNullable()**. These methods help in improving performance, avoiding infinite loops, and handling `null` values effectively.



