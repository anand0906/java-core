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

