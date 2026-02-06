# Java Collection Framework - Complete Guide

## Table of Contents
1. [Introduction](#introduction)
2. [Collection Framework Hierarchy](#collection-framework-hierarchy)
3. [Core Interfaces](#core-interfaces)
4. [List Interface](#list-interface)
5. [Set Interface](#set-interface)
6. [Queue Interface](#queue-interface)
7. [Map Interface](#map-interface)
8. [Iterator and ListIterator](#iterator-and-listiterator)
9. [Comparable and Comparator](#comparable-and-comparator)
10. [Collections Utility Class](#collections-utility-class)
11. [Arrays Utility Class](#arrays-utility-class)
12. [Legacy Classes](#legacy-classes)
13. [Concurrent Collections](#concurrent-collections)
14. [Best Practices](#best-practices)
15. [Performance Comparison](#performance-comparison)

---

## Introduction

The Java Collection Framework is a unified architecture for representing and manipulating collections of objects. It provides a set of interfaces, implementations, and algorithms to work with groups of objects efficiently.

### Key Benefits
- **Reduces programming effort** - Pre-built data structures and algorithms
- **Increases performance** - High-performance implementations
- **Interoperability** - Collections work well together
- **Reduces learning curve** - Consistent API design
- **Promotes software reuse** - Standard interfaces

### Framework Components
1. **Interfaces** - Abstract data types representing collections
2. **Implementations** - Concrete classes implementing collection interfaces
3. **Algorithms** - Methods for searching, sorting, and manipulating collections

---

## Collection Framework Hierarchy

```
                            Iterable<E>
                                |
                         Collection<E>
                                |
        +-------------------+---+-------------------+
        |                   |                       |
     List<E>             Set<E>                 Queue<E>
        |                   |                       |
        |            +------+------+         +------+------+
        |            |             |         |             |
        |       SortedSet<E>  HashSet   Deque<E>      PriorityQueue
        |            |                       |
        |       NavigableSet<E>         ArrayDeque
        |            |
        |       TreeSet
        |
    +---+---+
    |       |
ArrayList LinkedList


                            Map<E>
                              |
                    +---------+---------+
                    |                   |
                HashMap           SortedMap<E>
                    |                   |
                LinkedHashMap    NavigableMap<E>
                    |                   |
                Hashtable            TreeMap
                    |
               Properties
```

---

## Core Interfaces

### 1. Iterable Interface

The root interface for all collection classes. Allows objects to be iterated using for-each loop.

```java
public interface Iterable<T> {
    Iterator<T> iterator();
    
    default void forEach(Consumer<? super T> action) {
        // Perform action for each element
    }
    
    default Spliterator<T> spliterator() {
        // Returns a Spliterator for parallel processing
    }
}
```

**Usage Example:**
```java
List<String> list = Arrays.asList("A", "B", "C");
for (String item : list) {
    System.out.println(item);
}
```

### 2. Collection Interface

The root interface in the collection hierarchy. It represents a group of objects.

**Key Methods:**
```java
// Basic operations
boolean add(E e)
boolean remove(Object o)
boolean contains(Object o)
int size()
boolean isEmpty()
void clear()

// Bulk operations
boolean addAll(Collection<? extends E> c)
boolean removeAll(Collection<?> c)
boolean retainAll(Collection<?> c)
boolean containsAll(Collection<?> c)

// Array operations
Object[] toArray()
<T> T[] toArray(T[] a)

// Java 8+ methods
default Stream<E> stream()
default Stream<E> parallelStream()
default boolean removeIf(Predicate<? super E> filter)
```

---

## List Interface

An ordered collection (sequence) that allows duplicate elements. Elements can be accessed by their integer index.

### Key Characteristics
- Maintains insertion order
- Allows duplicate elements
- Allows null elements (implementation-dependent)
- Positional access to elements

### List Methods

```java
// Positional access
E get(int index)
E set(int index, E element)
void add(int index, E element)
E remove(int index)

// Search operations
int indexOf(Object o)
int lastIndexOf(Object o)

// List iterators
ListIterator<E> listIterator()
ListIterator<E> listIterator(int index)

// View operations
List<E> subList(int fromIndex, int toIndex)

// Java 8+ methods
default void replaceAll(UnaryOperator<E> operator)
default void sort(Comparator<? super E> c)
```

### ArrayList

Dynamic array implementation providing fast random access.

**Internal Working:**
- Backed by a dynamic array
- Default initial capacity: 10
- Grows by 50% when capacity is exceeded
- Not synchronized

```java
// Creation
List<String> arrayList = new ArrayList<>();
List<String> arrayListWithCapacity = new ArrayList<>(20);
List<String> arrayListFromCollection = new ArrayList<>(existingCollection);

// Common operations
arrayList.add("Element");
arrayList.add(0, "First Element");
String element = arrayList.get(2);
arrayList.set(1, "Updated Element");
arrayList.remove(0);
arrayList.remove("Element");

// Iteration
for (String item : arrayList) {
    System.out.println(item);
}

// Java 8 forEach
arrayList.forEach(item -> System.out.println(item));

// Stream operations
arrayList.stream()
    .filter(s -> s.startsWith("A"))
    .forEach(System.out::println);
```

**Time Complexity:**
- Access by index: O(1)
- Add at end: O(1) amortized
- Add at specific index: O(n)
- Remove: O(n)
- Contains: O(n)

**Best Use Cases:**
- Frequent random access
- Iteration over elements
- When array resizing is acceptable

### LinkedList

Doubly-linked list implementation providing efficient insertion and deletion.

**Internal Working:**
- Implemented as doubly-linked list
- Each node contains data and references to previous and next nodes
- Not synchronized

```java
// Creation
LinkedList<String> linkedList = new LinkedList<>();
LinkedList<String> linkedListFromCollection = new LinkedList<>(existingCollection);

// List operations
linkedList.add("Element");
linkedList.addFirst("First");
linkedList.addLast("Last");
linkedList.add(2, "At Index 2");

// Deque operations
linkedList.offerFirst("Offer First");
linkedList.offerLast("Offer Last");
String first = linkedList.pollFirst();
String last = linkedList.pollLast();
String peek = linkedList.peekFirst();

// Queue operations
linkedList.offer("Queue Element");
String polled = linkedList.poll();
String peeked = linkedList.peek();
```

**Time Complexity:**
- Access by index: O(n)
- Add at beginning/end: O(1)
- Add at specific index: O(n)
- Remove at beginning/end: O(1)
- Remove at specific index: O(n)

**Best Use Cases:**
- Frequent insertion/deletion at beginning or end
- Queue or deque implementation
- When random access is not needed

### Vector

Synchronized version of ArrayList (legacy class).

```java
Vector<String> vector = new Vector<>();
vector.add("Element");
vector.addElement("Legacy Method"); // Legacy method

// Synchronized methods
vector.capacity(); // Current capacity
vector.ensureCapacity(50);
```

**Differences from ArrayList:**
- Synchronized (thread-safe)
- Grows by 100% (doubles in size)
- Legacy class (use ArrayList instead)

### Stack

LIFO (Last-In-First-Out) data structure extending Vector.

```java
Stack<Integer> stack = new Stack<>();

// Push elements
stack.push(1);
stack.push(2);
stack.push(3);

// Pop element
Integer top = stack.pop(); // Returns and removes 3

// Peek element
Integer peek = stack.peek(); // Returns 2 without removing

// Search element
int position = stack.search(1); // Returns position from top

// Check if empty
boolean isEmpty = stack.empty();
```

**Note:** Use `Deque` interface with `ArrayDeque` instead of Stack for better performance.

---

## Set Interface

A collection that contains no duplicate elements. Models the mathematical set abstraction.

### Key Characteristics
- No duplicate elements
- At most one null element (implementation-dependent)
- No guaranteed order (except SortedSet implementations)

### HashSet

Hash table implementation of Set interface.

**Internal Working:**
- Backed by HashMap
- Uses hashCode() and equals() for uniqueness
- Default initial capacity: 16
- Default load factor: 0.75
- Not synchronized

```java
// Creation
Set<String> hashSet = new HashSet<>();
Set<String> hashSetWithCapacity = new HashSet<>(32);
Set<String> hashSetWithLoadFactor = new HashSet<>(32, 0.75f);

// Operations
hashSet.add("Element1");
hashSet.add("Element2");
hashSet.add("Element1"); // Duplicate, won't be added

boolean contains = hashSet.contains("Element1");
hashSet.remove("Element1");

// Iteration (no guaranteed order)
for (String item : hashSet) {
    System.out.println(item);
}

// Set operations
Set<String> set1 = new HashSet<>(Arrays.asList("A", "B", "C"));
Set<String> set2 = new HashSet<>(Arrays.asList("B", "C", "D"));

// Union
Set<String> union = new HashSet<>(set1);
union.addAll(set2); // [A, B, C, D]

// Intersection
Set<String> intersection = new HashSet<>(set1);
intersection.retainAll(set2); // [B, C]

// Difference
Set<String> difference = new HashSet<>(set1);
difference.removeAll(set2); // [A]
```

**Time Complexity:**
- Add: O(1)
- Remove: O(1)
- Contains: O(1)
- Iteration: O(capacity + size)

**Best Use Cases:**
- Fast lookups
- Ensuring uniqueness
- When order doesn't matter

### LinkedHashSet

Hash table and linked list implementation maintaining insertion order.

**Internal Working:**
- Extends HashSet
- Maintains doubly-linked list of entries
- Preserves insertion order

```java
Set<String> linkedHashSet = new LinkedHashSet<>();
linkedHashSet.add("First");
linkedHashSet.add("Second");
linkedHashSet.add("Third");

// Iteration maintains insertion order
for (String item : linkedHashSet) {
    System.out.println(item); // First, Second, Third
}
```

**Time Complexity:**
- Same as HashSet: O(1) for basic operations
- Slightly slower than HashSet due to linked list maintenance

**Best Use Cases:**
- When insertion order matters
- Predictable iteration order needed

### TreeSet

NavigableSet implementation based on TreeMap.

**Internal Working:**
- Backed by TreeMap (Red-Black tree)
- Elements sorted in natural order or by Comparator
- Not synchronized

```java
// Creation
Set<Integer> treeSet = new TreeSet<>();
Set<String> treeSetWithComparator = new TreeSet<>(Comparator.reverseOrder());

// Operations
treeSet.add(5);
treeSet.add(2);
treeSet.add(8);
treeSet.add(1);

// Elements are sorted: [1, 2, 5, 8]
for (Integer num : treeSet) {
    System.out.println(num);
}

// NavigableSet methods
Integer first = treeSet.first();        // 1
Integer last = treeSet.last();          // 8
Integer lower = treeSet.lower(5);       // 2
Integer higher = treeSet.higher(5);     // 8
Integer floor = treeSet.floor(6);       // 5
Integer ceiling = treeSet.ceiling(6);   // 8

// SubSet operations
SortedSet<Integer> headSet = treeSet.headSet(5);     // [1, 2]
SortedSet<Integer> tailSet = treeSet.tailSet(5);     // [5, 8]
SortedSet<Integer> subSet = treeSet.subSet(2, 8);    // [2, 5]

// Descending operations
NavigableSet<Integer> descending = treeSet.descendingSet();
Iterator<Integer> descendingIterator = treeSet.descendingIterator();

// Polling
Integer pollFirst = treeSet.pollFirst();  // Removes and returns 1
Integer pollLast = treeSet.pollLast();    // Removes and returns 8
```

**Time Complexity:**
- Add: O(log n)
- Remove: O(log n)
- Contains: O(log n)

**Best Use Cases:**
- Sorted elements required
- Range operations needed
- Navigation through elements

### EnumSet

Specialized Set implementation for enum types.

```java
enum Day {
    MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
}

// Creation
EnumSet<Day> weekdays = EnumSet.range(Day.MONDAY, Day.FRIDAY);
EnumSet<Day> weekend = EnumSet.of(Day.SATURDAY, Day.SUNDAY);
EnumSet<Day> allDays = EnumSet.allOf(Day.class);
EnumSet<Day> noDays = EnumSet.noneOf(Day.class);
EnumSet<Day> weekdaysComplement = EnumSet.complementOf(weekend);

// Operations
weekdays.add(Day.SATURDAY);
boolean isWeekday = weekdays.contains(Day.MONDAY);
```

**Characteristics:**
- Extremely efficient (bit vector implementation)
- Type-safe
- Cannot contain null
- Faster than HashSet for enums

---

## Queue Interface

A collection designed for holding elements prior to processing. Typically orders elements in FIFO manner.

### Queue Methods

```java
// Insertion
boolean add(E e)      // Throws exception if fails
boolean offer(E e)    // Returns false if fails

// Removal
E remove()            // Throws exception if empty
E poll()              // Returns null if empty

// Examination
E element()           // Throws exception if empty
E peek()              // Returns null if empty
```

### PriorityQueue

Priority heap implementation of Queue interface.

**Internal Working:**
- Backed by balanced binary heap
- Elements ordered by natural ordering or Comparator
- Head is the least element
- Not synchronized

```java
// Creation
Queue<Integer> pq = new PriorityQueue<>();
Queue<Integer> maxHeap = new PriorityQueue<>(Comparator.reverseOrder());

// Custom comparator
Queue<String> stringPQ = new PriorityQueue<>((a, b) -> 
    Integer.compare(a.length(), b.length()));

// Operations
pq.offer(5);
pq.offer(2);
pq.offer(8);
pq.offer(1);

Integer head = pq.peek();    // 1 (smallest)
Integer removed = pq.poll(); // Removes 1
// Remaining: [2, 5, 8]

// Custom object example
class Task implements Comparable<Task> {
    String name;
    int priority;
    
    public Task(String name, int priority) {
        this.name = name;
        this.priority = priority;
    }
    
    @Override
    public int compareTo(Task other) {
        return Integer.compare(this.priority, other.priority);
    }
}

Queue<Task> taskQueue = new PriorityQueue<>();
taskQueue.offer(new Task("Low Priority", 3));
taskQueue.offer(new Task("High Priority", 1));
taskQueue.offer(new Task("Medium Priority", 2));

Task nextTask = taskQueue.poll(); // High Priority task
```

**Time Complexity:**
- Offer: O(log n)
- Poll: O(log n)
- Peek: O(1)
- Remove (specific element): O(n)

**Best Use Cases:**
- Task scheduling
- Dijkstra's algorithm
- Huffman coding

### Deque Interface

Double-ended queue supporting element insertion and removal at both ends.

**Deque Methods:**
```java
// Insert at front
void addFirst(E e)
boolean offerFirst(E e)

// Insert at end
void addLast(E e)
boolean offerLast(E e)

// Remove from front
E removeFirst()
E pollFirst()

// Remove from end
E removeLast()
E pollLast()

// Examine front
E getFirst()
E peekFirst()

// Examine end
E getLast()
E peekLast()
```

### ArrayDeque

Resizable-array implementation of Deque interface.

**Internal Working:**
- Backed by circular array
- No capacity restrictions
- Faster than Stack and LinkedList
- Not thread-safe

```java
// Creation
Deque<String> deque = new ArrayDeque<>();

// Add elements
deque.addFirst("First");
deque.addLast("Last");
deque.offerFirst("New First");
deque.offerLast("New Last");

// Remove elements
String first = deque.pollFirst();
String last = deque.pollLast();

// Peek elements
String peekFirst = deque.peekFirst();
String peekLast = deque.peekLast();

// Use as Stack
Deque<Integer> stack = new ArrayDeque<>();
stack.push(1);
stack.push(2);
stack.push(3);
Integer top = stack.pop(); // 3

// Use as Queue
Deque<String> queue = new ArrayDeque<>();
queue.offer("First");
queue.offer("Second");
String polled = queue.poll(); // First
```

**Time Complexity:**
- Add/Remove at both ends: O(1) amortized
- Access by index: Not supported
- Contains: O(n)

**Best Use Cases:**
- Stack implementation (better than Stack class)
- Queue implementation
- Double-ended operations

---

## Map Interface

An object that maps keys to values. Cannot contain duplicate keys; each key maps to at most one value.

### Key Characteristics
- Not a true collection (doesn't extend Collection interface)
- Maps keys to values
- No duplicate keys allowed
- Each key maps to at most one value

### Map Methods

```java
// Basic operations
V put(K key, V value)
V get(Object key)
V remove(Object key)
boolean containsKey(Object key)
boolean containsValue(Object value)
int size()
boolean isEmpty()

// Bulk operations
void putAll(Map<? extends K, ? extends V> m)
void clear()

// Collection views
Set<K> keySet()
Collection<V> values()
Set<Map.Entry<K, V>> entrySet()

// Java 8+ methods
V getOrDefault(Object key, V defaultValue)
V putIfAbsent(K key, V value)
boolean remove(Object key, Object value)
boolean replace(K key, V oldValue, V newValue)
V replace(K key, V value)
void forEach(BiConsumer<? super K, ? super V> action)
V compute(K key, BiFunction<? super K, ? super V, ? extends V> remappingFunction)
V computeIfAbsent(K key, Function<? super K, ? extends V> mappingFunction)
V computeIfPresent(K key, BiFunction<? super K, ? super V, ? extends V> remappingFunction)
V merge(K key, V value, BiFunction<? super V, ? super V, ? extends V> remappingFunction)
void replaceAll(BiFunction<? super K, ? super V, ? extends V> function)
```

### HashMap

Hash table implementation of Map interface.

**Internal Working:**
- Array of buckets (default 16)
- Uses hashCode() and equals() for key operations
- Load factor: 0.75 (resize when 75% full)
- Handles collisions using linked lists (or trees if > 8 elements in bucket)
- Not synchronized

```java
// Creation
Map<String, Integer> hashMap = new HashMap<>();
Map<String, Integer> hashMapWithCapacity = new HashMap<>(32);
Map<String, Integer> hashMapFromMap = new HashMap<>(existingMap);

// Basic operations
hashMap.put("One", 1);
hashMap.put("Two", 2);
hashMap.put("Three", 3);

Integer value = hashMap.get("One");
boolean hasKey = hashMap.containsKey("Two");
boolean hasValue = hashMap.containsValue(3);

hashMap.remove("Three");

// Java 8+ methods
hashMap.putIfAbsent("Four", 4);
hashMap.computeIfAbsent("Five", k -> 5);
hashMap.computeIfPresent("One", (k, v) -> v + 10);
hashMap.merge("Two", 20, (oldVal, newVal) -> oldVal + newVal);

Integer defaultValue = hashMap.getOrDefault("Six", 0);

// Iteration
// Using entrySet (most efficient)
for (Map.Entry<String, Integer> entry : hashMap.entrySet()) {
    System.out.println(entry.getKey() + ": " + entry.getValue());
}

// Using keySet
for (String key : hashMap.keySet()) {
    System.out.println(key + ": " + hashMap.get(key));
}

// Using values
for (Integer val : hashMap.values()) {
    System.out.println(val);
}

// Java 8 forEach
hashMap.forEach((key, value) -> 
    System.out.println(key + ": " + value));

// Stream operations
hashMap.entrySet().stream()
    .filter(e -> e.getValue() > 2)
    .forEach(e -> System.out.println(e.getKey()));
```

**Time Complexity:**
- Get: O(1) average, O(n) worst case
- Put: O(1) average, O(n) worst case
- Remove: O(1) average, O(n) worst case
- ContainsKey: O(1) average

**Best Use Cases:**
- Fast key-value lookups
- Caching
- Frequency counting

### LinkedHashMap

Hash table and linked list implementation maintaining insertion order.

**Internal Working:**
- Extends HashMap
- Maintains doubly-linked list of entries
- Can maintain access order instead of insertion order

```java
// Creation (insertion order)
Map<String, Integer> linkedHashMap = new LinkedHashMap<>();

// Creation (access order) - useful for LRU cache
Map<String, Integer> accessOrderMap = new LinkedHashMap<>(16, 0.75f, true);

// Operations
linkedHashMap.put("First", 1);
linkedHashMap.put("Second", 2);
linkedHashMap.put("Third", 3);

// Iteration maintains insertion order
for (Map.Entry<String, Integer> entry : linkedHashMap.entrySet()) {
    System.out.println(entry.getKey()); // First, Second, Third
}

// LRU Cache implementation
class LRUCache<K, V> extends LinkedHashMap<K, V> {
    private int capacity;
    
    public LRUCache(int capacity) {
        super(capacity, 0.75f, true);
        this.capacity = capacity;
    }
    
    @Override
    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        return size() > capacity;
    }
}

LRUCache<String, String> cache = new LRUCache<>(3);
cache.put("1", "One");
cache.put("2", "Two");
cache.put("3", "Three");
cache.get("1"); // Access
cache.put("4", "Four"); // "2" is removed (least recently used)
```

**Time Complexity:**
- Same as HashMap: O(1) for basic operations

**Best Use Cases:**
- Predictable iteration order
- LRU cache implementation
- Maintaining insertion/access order

### TreeMap

NavigableMap implementation based on Red-Black tree.

**Internal Working:**
- Red-Black tree (self-balancing BST)
- Keys sorted by natural ordering or Comparator
- Not synchronized

```java
// Creation
Map<Integer, String> treeMap = new TreeMap<>();
Map<String, Integer> reverseTreeMap = new TreeMap<>(Comparator.reverseOrder());

// Operations
treeMap.put(3, "Three");
treeMap.put(1, "One");
treeMap.put(2, "Two");
treeMap.put(5, "Five");
treeMap.put(4, "Four");

// Sorted iteration
for (Map.Entry<Integer, String> entry : treeMap.entrySet()) {
    System.out.println(entry.getKey()); // 1, 2, 3, 4, 5
}

// NavigableMap methods
Integer firstKey = treeMap.firstKey();           // 1
Integer lastKey = treeMap.lastKey();             // 5
Integer lowerKey = treeMap.lowerKey(3);          // 2
Integer higherKey = treeMap.higherKey(3);        // 4
Integer floorKey = treeMap.floorKey(3);          // 3
Integer ceilingKey = treeMap.ceilingKey(3);      // 3

Map.Entry<Integer, String> firstEntry = treeMap.firstEntry();
Map.Entry<Integer, String> lastEntry = treeMap.lastEntry();
Map.Entry<Integer, String> pollFirstEntry = treeMap.pollFirstEntry();
Map.Entry<Integer, String> pollLastEntry = treeMap.pollLastEntry();

// SubMap operations
SortedMap<Integer, String> headMap = treeMap.headMap(3);    // Keys < 3
SortedMap<Integer, String> tailMap = treeMap.tailMap(3);    // Keys >= 3
SortedMap<Integer, String> subMap = treeMap.subMap(2, 5);   // 2 <= keys < 5

// Descending operations
NavigableMap<Integer, String> descendingMap = treeMap.descendingMap();
NavigableSet<Integer> descendingKeySet = treeMap.descendingKeySet();
```

**Time Complexity:**
- Get: O(log n)
- Put: O(log n)
- Remove: O(log n)
- ContainsKey: O(log n)

**Best Use Cases:**
- Sorted key-value pairs
- Range queries
- Navigation through keys

### Hashtable

Synchronized hash table implementation (legacy class).

```java
Hashtable<String, Integer> hashtable = new Hashtable<>();
hashtable.put("Key", 1);
// hashtable.put(null, 1);    // NullPointerException
// hashtable.put("Key", null); // NullPointerException

// Thread-safe operations
Integer value = hashtable.get("Key");
```

**Differences from HashMap:**
- Synchronized (thread-safe)
- Doesn't allow null keys or values
- Legacy class (use HashMap or ConcurrentHashMap)

### WeakHashMap

Hash table implementation with weak references for keys.

```java
WeakHashMap<Object, String> weakHashMap = new WeakHashMap<>();

Object key1 = new Object();
Object key2 = new Object();

weakHashMap.put(key1, "Value1");
weakHashMap.put(key2, "Value2");

System.out.println(weakHashMap.size()); // 2

key1 = null; // Remove strong reference
System.gc(); // Suggest garbage collection

// key1 entry may be removed after GC
```

**Best Use Cases:**
- Caching scenarios
- Memory-sensitive applications
- Preventing memory leaks

### IdentityHashMap

Hash table using reference equality (==) instead of object equality.

```java
IdentityHashMap<String, Integer> identityMap = new IdentityHashMap<>();

String s1 = new String("Key");
String s2 = new String("Key");

identityMap.put(s1, 1);
identityMap.put(s2, 2);

System.out.println(identityMap.size()); // 2 (different references)
```

**Best Use Cases:**
- Serialization/deserialization
- Object graph traversal
- Proxy pattern implementations

### EnumMap

Specialized Map implementation for enum keys.

```java
enum Size {
    SMALL, MEDIUM, LARGE, EXTRA_LARGE
}

EnumMap<Size, String> enumMap = new EnumMap<>(Size.class);
enumMap.put(Size.SMALL, "S");
enumMap.put(Size.MEDIUM, "M");
enumMap.put(Size.LARGE, "L");

// Iteration in natural order of enum constants
for (Map.Entry<Size, String> entry : enumMap.entrySet()) {
    System.out.println(entry.getKey() + ": " + entry.getValue());
}
```

**Characteristics:**
- Extremely efficient (array-based)
- Natural ordering
- Cannot contain null keys
- Faster than HashMap for enums

---

## Iterator and ListIterator

### Iterator Interface

Universal iterator for traversing collections.

```java
public interface Iterator<E> {
    boolean hasNext();
    E next();
    void remove(); // Optional operation
    default void forEachRemaining(Consumer<? super E> action);
}
```

**Usage:**
```java
List<String> list = Arrays.asList("A", "B", "C", "D");
Iterator<String> iterator = list.iterator();

while (iterator.hasNext()) {
    String element = iterator.next();
    System.out.println(element);
    
    // Remove element while iterating (if supported)
    if (element.equals("B")) {
        iterator.remove();
    }
}

// Java 8 forEachRemaining
iterator = list.iterator();
iterator.forEachRemaining(System.out::println);
```

**Key Points:**
- Only forward traversal
- Can remove elements during iteration
- Fail-fast (throws ConcurrentModificationException if collection modified)

### ListIterator Interface

Bidirectional iterator for List implementations.

```java
public interface ListIterator<E> extends Iterator<E> {
    // Forward traversal
    boolean hasNext();
    E next();
    int nextIndex();
    
    // Backward traversal
    boolean hasPrevious();
    E previous();
    int previousIndex();
    
    // Modification
    void remove();
    void set(E e);
    void add(E e);
}
```

**Usage:**
```java
List<String> list = new ArrayList<>(Arrays.asList("A", "B", "C", "D"));
ListIterator<String> listIterator = list.listIterator();

// Forward traversal
while (listIterator.hasNext()) {
    int index = listIterator.nextIndex();
    String element = listIterator.next();
    System.out.println(index + ": " + element);
    
    // Modify element
    if (element.equals("B")) {
        listIterator.set("B_MODIFIED");
    }
    
    // Add element after current
    if (element.equals("C")) {
        listIterator.add("C_ADDED");
    }
}

// Backward traversal
while (listIterator.hasPrevious()) {
    String element = listIterator.previous();
    System.out.println(element);
}

// Start from specific index
ListIterator<String> fromIndex = list.listIterator(2);
```

**Key Points:**
- Bidirectional traversal
- Can modify list during iteration
- Can add elements during iteration
- Provides element indices

### Spliterator Interface

Iterator for parallel processing (Java 8+).

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8);
Spliterator<Integer> spliterator = numbers.spliterator();

// Characteristics
int characteristics = spliterator.characteristics();
long estimatedSize = spliterator.estimateSize();

// Try advance
spliterator.tryAdvance(num -> System.out.println(num)); // Prints 1

// Try split for parallel processing
Spliterator<Integer> split = spliterator.trySplit();

// For each remaining
spliterator.forEachRemaining(System.out::println);
```

---

## Comparable and Comparator

### Comparable Interface

Natural ordering for objects.

```java
public interface Comparable<T> {
    int compareTo(T o);
}
```

**Implementation:**
```java
class Student implements Comparable<Student> {
    private String name;
    private int age;
    private double gpa;
    
    public Student(String name, int age, double gpa) {
        this.name = name;
        this.age = age;
        this.gpa = gpa;
    }
    
    @Override
    public int compareTo(Student other) {
        // Natural ordering by GPA (descending)
        return Double.compare(other.gpa, this.gpa);
    }
    
    @Override
    public String toString() {
        return name + " (GPA: " + gpa + ")";
    }
}

// Usage
List<Student> students = new ArrayList<>();
students.add(new Student("Alice", 20, 3.8));
students.add(new Student("Bob", 22, 3.5));
students.add(new Student("Charlie", 21, 3.9));

Collections.sort(students); // Uses compareTo
System.out.println(students); // Sorted by GPA descending
```

### Comparator Interface

Custom ordering for objects.

```java
@FunctionalInterface
public interface Comparator<T> {
    int compare(T o1, T o2);
    
    // Default methods
    default Comparator<T> reversed();
    default Comparator<T> thenComparing(Comparator<? super T> other);
    default Comparator<T> thenComparingInt(ToIntFunction<? super T> keyExtractor);
    default Comparator<T> thenComparingLong(ToLongFunction<? super T> keyExtractor);
    default Comparator<T> thenComparingDouble(ToDoubleFunction<? super T> keyExtractor);
    
    // Static methods
    static <T extends Comparable<? super T>> Comparator<T> naturalOrder();
    static <T extends Comparable<? super T>> Comparator<T> reverseOrder();
    static <T> Comparator<T> nullsFirst(Comparator<? super T> comparator);
    static <T> Comparator<T> nullsLast(Comparator<? super T> comparator);
    static <T, U extends Comparable<? super U>> Comparator<T> comparing(
        Function<? super T, ? extends U> keyExtractor);
    static <T> Comparator<T> comparingInt(ToIntFunction<? super T> keyExtractor);
    static <T> Comparator<T> comparingLong(ToLongFunction<? super T> keyExtractor);
    static <T> Comparator<T> comparingDouble(ToDoubleFunction<? super T> keyExtractor);
}
```

**Implementation Examples:**

```java
class Employee {
    String name;
    int age;
    double salary;
    String department;
    
    public Employee(String name, int age, double salary, String department) {
        this.name = name;
        this.age = age;
        this.salary = salary;
        this.department = department;
    }
}

List<Employee> employees = new ArrayList<>();
// ... add employees

// 1. Anonymous class
Collections.sort(employees, new Comparator<Employee>() {
    @Override
    public int compare(Employee e1, Employee e2) {
        return e1.name.compareTo(e2.name);
    }
});

// 2. Lambda expression
Collections.sort(employees, (e1, e2) -> e1.name.compareTo(e2.name));

// 3. Method reference with comparing
Collections.sort(employees, Comparator.comparing(Employee::getName));

// 4. Multiple sorting criteria
Collections.sort(employees, 
    Comparator.comparing(Employee::getDepartment)
              .thenComparing(Employee::getSalary, Comparator.reverseOrder())
              .thenComparing(Employee::getName));

// 5. Null-safe comparator
Collections.sort(employees, 
    Comparator.nullsLast(Comparator.comparing(Employee::getName)));

// 6. Primitive comparators
Collections.sort(employees, Comparator.comparingInt(Employee::getAge));
Collections.sort(employees, Comparator.comparingDouble(Employee::getSalary));

// 7. Reversed comparator
Collections.sort(employees, 
    Comparator.comparing(Employee::getSalary).reversed());

// 8. Natural order
List<String> names = Arrays.asList("Charlie", "Alice", "Bob");
Collections.sort(names, Comparator.naturalOrder());

// 9. Reverse natural order
Collections.sort(names, Comparator.reverseOrder());

// 10. Stream sorting
List<Employee> sortedEmployees = employees.stream()
    .sorted(Comparator.comparing(Employee::getDepartment)
                      .thenComparingDouble(Employee::getSalary))
    .collect(Collectors.toList());
```

**Comparator vs Comparable:**

| Aspect | Comparable | Comparator |
|--------|-----------|------------|
| Location | Within the class | Separate class/lambda |
| Method | compareTo(T o) | compare(T o1, T o2) |
| Sorting logic | Single natural ordering | Multiple custom orderings |
| Modification | Requires class modification | No class modification needed |
| Usage | Collections.sort(list) | Collections.sort(list, comparator) |

---

## Collections Utility Class

Utility class with static methods for working with collections.

### Sorting Methods

```java
List<Integer> numbers = Arrays.asList(5, 2, 8, 1, 9);

// Sort in natural order
Collections.sort(numbers);

// Sort with comparator
Collections.sort(numbers, Comparator.reverseOrder());

// Sort custom objects
List<String> names = Arrays.asList("Charlie", "Alice", "Bob");
Collections.sort(names, String.CASE_INSENSITIVE_ORDER);
```

### Searching Methods

```java
List<Integer> sortedList = Arrays.asList(1, 3, 5, 7, 9);

// Binary search (list must be sorted)
int index = Collections.binarySearch(sortedList, 5); // Returns 2
int notFound = Collections.binarySearch(sortedList, 6); // Returns -(insertion point) - 1

// Binary search with comparator
List<String> sortedNames = Arrays.asList("Alice", "Bob", "Charlie");
int nameIndex = Collections.binarySearch(sortedNames, "Bob", String.CASE_INSENSITIVE_ORDER);
```

### Manipulation Methods

```java
List<Integer> list = new ArrayList<>(Arrays.asList(1, 2, 3, 4, 5));

// Reverse
Collections.reverse(list); // [5, 4, 3, 2, 1]

// Shuffle
Collections.shuffle(list); // Randomizes order

// Rotate
Collections.rotate(list, 2); // Rotates elements by distance

// Swap
Collections.swap(list, 0, 4); // Swaps elements at indices

// Fill
Collections.fill(list, 0); // Fills all positions with 0

// Copy
List<Integer> dest = new ArrayList<>(Arrays.asList(0, 0, 0, 0, 0));
Collections.copy(dest, Arrays.asList(1, 2, 3)); // dest = [1, 2, 3, 0, 0]

// Replace all
Collections.replaceAll(list, 0, 10); // Replaces all 0s with 10
```

### Min/Max Methods

```java
List<Integer> numbers = Arrays.asList(5, 2, 8, 1, 9);

// Find min/max
Integer min = Collections.min(numbers);
Integer max = Collections.max(numbers);

// With comparator
String minString = Collections.min(
    Arrays.asList("apple", "pie", "cherry"), 
    Comparator.comparingInt(String::length)
);
```

### Frequency and Disjoint

```java
List<String> list = Arrays.asList("A", "B", "A", "C", "A", "B");

// Count occurrences
int frequency = Collections.frequency(list, "A"); // 3

// Check if disjoint (no common elements)
List<String> list1 = Arrays.asList("A", "B", "C");
List<String> list2 = Arrays.asList("D", "E", "F");
boolean disjoint = Collections.disjoint(list1, list2); // true
```

### Unmodifiable Collections

```java
List<String> list = new ArrayList<>(Arrays.asList("A", "B", "C"));

// Unmodifiable wrappers
List<String> unmodifiableList = Collections.unmodifiableList(list);
Set<String> unmodifiableSet = Collections.unmodifiableSet(new HashSet<>(list));
Map<String, Integer> unmodifiableMap = Collections.unmodifiableMap(new HashMap<>());

// Attempting to modify throws UnsupportedOperationException
// unmodifiableList.add("D"); // Throws exception

// Java 9+ factory methods (truly immutable)
List<String> immutableList = List.of("A", "B", "C");
Set<String> immutableSet = Set.of("A", "B", "C");
Map<String, Integer> immutableMap = Map.of("A", 1, "B", 2);
```

### Synchronized Collections

```java
List<String> list = new ArrayList<>();
Set<String> set = new HashSet<>();
Map<String, Integer> map = new HashMap<>();

// Synchronized wrappers (thread-safe)
List<String> syncList = Collections.synchronizedList(list);
Set<String> syncSet = Collections.synchronizedSet(set);
Map<String, Integer> syncMap = Collections.synchronizedMap(map);

// Manual synchronization needed for iteration
synchronized (syncList) {
    Iterator<String> iterator = syncList.iterator();
    while (iterator.hasNext()) {
        System.out.println(iterator.next());
    }
}
```

### Checked Collections

```java
// Type-safe collections (runtime type checking)
List<String> checkedList = Collections.checkedList(
    new ArrayList<>(), String.class);

Set<Integer> checkedSet = Collections.checkedSet(
    new HashSet<>(), Integer.class);

Map<String, Integer> checkedMap = Collections.checkedMap(
    new HashMap<>(), String.class, Integer.class);

// Prevents ClassCastException at runtime
```

### Empty and Singleton Collections

```java
// Empty immutable collections
List<String> emptyList = Collections.emptyList();
Set<String> emptySet = Collections.emptySet();
Map<String, Integer> emptyMap = Collections.emptyMap();
Iterator<String> emptyIterator = Collections.emptyIterator();

// Singleton (immutable single-element)
List<String> singletonList = Collections.singletonList("Only");
Set<String> singletonSet = Collections.singleton("Only");
Map<String, Integer> singletonMap = Collections.singletonMap("Key", 1);

// NCopies (immutable n copies)
List<String> nCopies = Collections.nCopies(5, "Repeat"); // ["Repeat" x5]
```

### Other Utility Methods

```java
// Add all
List<String> list = new ArrayList<>();
Collections.addAll(list, "A", "B", "C");

// Index of sublist
List<Integer> source = Arrays.asList(1, 2, 3, 4, 5, 3, 4);
List<Integer> target = Arrays.asList(3, 4);
int index = Collections.indexOfSubList(source, target); // 2
int lastIndex = Collections.lastIndexOfSubList(source, target); // 5

// As LIFO queue
Deque<String> stack = Collections.asLifoQueue(new ArrayDeque<>());

// New set from map
Set<String> set = Collections.newSetFromMap(new ConcurrentHashMap<>());
```

---

## Arrays Utility Class

Utility class for array operations.

### Sorting

```java
int[] numbers = {5, 2, 8, 1, 9};

// Sort entire array
Arrays.sort(numbers); // [1, 2, 5, 8, 9]

// Sort range
Arrays.sort(numbers, 1, 4); // Sort indices 1 to 3

// Sort objects
String[] names = {"Charlie", "Alice", "Bob"};
Arrays.sort(names); // Natural order

// Sort with comparator
Arrays.sort(names, Comparator.reverseOrder());

// Parallel sort (uses Fork/Join framework)
int[] largeArray = new int[1000000];
Arrays.parallelSort(largeArray);
```

### Searching

```java
int[] sortedArray = {1, 3, 5, 7, 9};

// Binary search (array must be sorted)
int index = Arrays.binarySearch(sortedArray, 5); // Returns 2
int notFound = Arrays.binarySearch(sortedArray, 6); // Returns -(insertion point) - 1

// Search in range
int rangeIndex = Arrays.binarySearch(sortedArray, 1, 4, 5);
```

### Equality and Comparison

```java
int[] array1 = {1, 2, 3};
int[] array2 = {1, 2, 3};
int[] array3 = {1, 2, 4};

// Equality
boolean equals = Arrays.equals(array1, array2); // true

// Deep equality (for multidimensional arrays)
int[][] matrix1 = {{1, 2}, {3, 4}};
int[][] matrix2 = {{1, 2}, {3, 4}};
boolean deepEquals = Arrays.deepEquals(matrix1, matrix2); // true

// Compare (lexicographically)
int comparison = Arrays.compare(array1, array3); // Negative value

// Mismatch (finds first differing index)
int mismatchIndex = Arrays.mismatch(array1, array3); // 2
```

### Fill and SetAll

```java
int[] array = new int[5];

// Fill with value
Arrays.fill(array, 10); // [10, 10, 10, 10, 10]

// Fill range
Arrays.fill(array, 1, 4, 20); // [10, 20, 20, 20, 10]

// Set all using generator
Arrays.setAll(array, i -> i * 2); // [0, 2, 4, 6, 8]

// Parallel set all
Arrays.parallelSetAll(array, i -> i * i); // [0, 1, 4, 9, 16]
```

### Copy Operations

```java
int[] original = {1, 2, 3, 4, 5};

// Copy of array
int[] copy = Arrays.copyOf(original, original.length);

// Copy with different length
int[] expanded = Arrays.copyOf(original, 10); // Pads with 0s
int[] truncated = Arrays.copyOf(original, 3); // [1, 2, 3]

// Copy range
int[] rangeCopy = Arrays.copyOfRange(original, 1, 4); // [2, 3, 4]
```

### String Representation

```java
int[] array = {1, 2, 3, 4, 5};

// String representation
String str = Arrays.toString(array); // "[1, 2, 3, 4, 5]"

// Deep string (for multidimensional)
int[][] matrix = {{1, 2}, {3, 4}};
String deepStr = Arrays.deepToString(matrix); // "[[1, 2], [3, 4]]"
```

### Hash Code

```java
int[] array = {1, 2, 3};

// Hash code
int hash = Arrays.hashCode(array);

// Deep hash code (for multidimensional)
int[][] matrix = {{1, 2}, {3, 4}};
int deepHash = Arrays.deepHashCode(matrix);
```

### List Conversion

```java
// Array to List (fixed-size)
Integer[] array = {1, 2, 3, 4, 5};
List<Integer> list = Arrays.asList(array);

// Modifiable list
List<Integer> modifiableList = new ArrayList<>(Arrays.asList(array));

// Primitive to list (requires boxing)
int[] primitiveArray = {1, 2, 3};
List<Integer> primitiveList = Arrays.stream(primitiveArray)
    .boxed()
    .collect(Collectors.toList());
```

### Stream Operations

```java
int[] numbers = {1, 2, 3, 4, 5};

// Create stream
IntStream stream = Arrays.stream(numbers);

// Stream with range
IntStream rangeStream = Arrays.stream(numbers, 1, 4);

// Parallel stream
IntStream parallelStream = Arrays.stream(numbers).parallel();

// Stream operations
int sum = Arrays.stream(numbers).sum();
double average = Arrays.stream(numbers).average().orElse(0);
int max = Arrays.stream(numbers).max().orElse(0);
```

---

## Legacy Classes

### Vector

Synchronized dynamic array (legacy).

```java
Vector<String> vector = new Vector<>();
vector.add("Element");
vector.addElement("Legacy add");

// Vector-specific methods
int capacity = vector.capacity();
vector.ensureCapacity(50);
vector.trimToSize();

Enumeration<String> enumeration = vector.elements();
while (enumeration.hasMoreElements()) {
    System.out.println(enumeration.nextElement());
}
```

**Modern Alternative:** ArrayList with Collections.synchronizedList() or concurrent collections.

### Stack

LIFO stack extending Vector (legacy).

```java
Stack<Integer> stack = new Stack<>();
stack.push(1);
stack.push(2);
Integer top = stack.pop();
Integer peek = stack.peek();
boolean empty = stack.empty();
int position = stack.search(1);
```

**Modern Alternative:** ArrayDeque (implements Deque interface).

### Hashtable

Synchronized hash table (legacy).

```java
Hashtable<String, Integer> hashtable = new Hashtable<>();
hashtable.put("Key", 1);
Integer value = hashtable.get("Key");

Enumeration<String> keys = hashtable.keys();
Enumeration<Integer> values = hashtable.elements();
```

**Modern Alternative:** HashMap or ConcurrentHashMap.

### Enumeration Interface

Legacy iterator interface.

```java
Vector<String> vector = new Vector<>(Arrays.asList("A", "B", "C"));
Enumeration<String> enumeration = vector.elements();

while (enumeration.hasMoreElements()) {
    String element = enumeration.nextElement();
    System.out.println(element);
}
```

**Modern Alternative:** Iterator interface.

### Properties

Hashtable extension for property lists.

```java
Properties properties = new Properties();

// Set properties
properties.setProperty("username", "admin");
properties.setProperty("password", "secret");

// Get properties
String username = properties.getProperty("username");
String timeout = properties.getProperty("timeout", "30"); // With default

// Load from file
try (FileInputStream fis = new FileInputStream("config.properties")) {
    properties.load(fis);
}

// Save to file
try (FileOutputStream fos = new FileOutputStream("config.properties")) {
    properties.store(fos, "Configuration");
}

// Load from XML
try (FileInputStream fis = new FileInputStream("config.xml")) {
    properties.loadFromXML(fis);
}

// List properties
properties.list(System.out);
```

---

## Concurrent Collections

Thread-safe collections for concurrent programming (java.util.concurrent).

### CopyOnWriteArrayList

Thread-safe ArrayList variant where all mutative operations create a fresh copy.

```java
CopyOnWriteArrayList<String> list = new CopyOnWriteArrayList<>();

list.add("A");
list.add("B");
list.add("C");

// Iteration never throws ConcurrentModificationException
for (String item : list) {
    // Safe even if another thread modifies the list
    System.out.println(item);
}

// Thread-safe iterator
Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
    System.out.println(iterator.next());
    // iterator.remove(); // Not supported
}
```

**Characteristics:**
- Expensive writes (creates copy)
- Fast reads (no locking)
- Iterator snapshot (won't see modifications)
- Best for read-heavy scenarios

### CopyOnWriteArraySet

Thread-safe Set backed by CopyOnWriteArrayList.

```java
CopyOnWriteArraySet<String> set = new CopyOnWriteArraySet<>();
set.add("A");
set.add("B");
set.add("A"); // Duplicate ignored

// Safe concurrent iteration
for (String item : set) {
    System.out.println(item);
}
```

### ConcurrentHashMap

Highly concurrent HashMap implementation.

```java
ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();

// Basic operations (thread-safe)
map.put("Key", 1);
Integer value = map.get("Key");
map.remove("Key");

// Atomic operations
map.putIfAbsent("Key", 1);
map.replace("Key", 1, 2); // Replace only if current value is 1
map.computeIfAbsent("Key", k -> 1);
map.computeIfPresent("Key", (k, v) -> v + 1);
map.merge("Key", 1, (oldVal, newVal) -> oldVal + newVal);

// Bulk operations (parallelizable)
map.forEach((key, value) -> System.out.println(key + ": " + value));

map.forEach(2, (key, value) -> 
    System.out.println(key + ": " + value)); // Parallel threshold

// Search operations
String result = map.search(2, (key, value) -> 
    value > 10 ? key : null);

// Reduce operations
Integer sum = map.reduce(2, (key, value) -> value,
    (v1, v2) -> v1 + v2);

// Atomic counter
ConcurrentHashMap<String, LongAdder> counters = new ConcurrentHashMap<>();
counters.computeIfAbsent("counter", k -> new LongAdder()).increment();
```

**Characteristics:**
- Lock striping (segment-level locking)
- Better concurrency than Hashtable
- Weakly consistent iterators
- No null keys or values

### ConcurrentSkipListMap

Concurrent sorted map implementation.

```java
ConcurrentSkipListMap<Integer, String> map = new ConcurrentSkipListMap<>();

map.put(3, "Three");
map.put(1, "One");
map.put(2, "Two");

// NavigableMap methods (thread-safe)
Integer firstKey = map.firstKey();
Integer lastKey = map.lastKey();
Map.Entry<Integer, String> firstEntry = map.firstEntry();

// Concurrent iteration in sorted order
for (Map.Entry<Integer, String> entry : map.entrySet()) {
    System.out.println(entry.getKey() + ": " + entry.getValue());
}
```

**Characteristics:**
- Lock-free implementation
- O(log n) operations
- Sorted order maintained
- Weakly consistent iterators

### ConcurrentSkipListSet

Concurrent sorted set backed by ConcurrentSkipListMap.

```java
ConcurrentSkipListSet<Integer> set = new ConcurrentSkipListSet<>();

set.add(5);
set.add(2);
set.add(8);

// NavigableSet methods (thread-safe)
Integer first = set.first();
Integer last = set.last();
Integer lower = set.lower(5);
```

### ConcurrentLinkedQueue

Unbounded thread-safe queue based on linked nodes.

```java
ConcurrentLinkedQueue<String> queue = new ConcurrentLinkedQueue<>();

// Thread-safe operations
queue.offer("A");
queue.offer("B");
queue.offer("C");

String head = queue.poll();
String peek = queue.peek();
int size = queue.size(); // Expensive O(n) operation
```

**Characteristics:**
- Lock-free implementation
- FIFO ordering
- Weakly consistent iterators
- Size is expensive

### ConcurrentLinkedDeque

Unbounded thread-safe double-ended queue.

```java
ConcurrentLinkedDeque<String> deque = new ConcurrentLinkedDeque<>();

deque.offerFirst("First");
deque.offerLast("Last");

String first = deque.pollFirst();
String last = deque.pollLast();
```

### BlockingQueue Interface

Queue supporting blocking operations.

**Implementations:**
1. ArrayBlockingQueue - Bounded blocking queue
2. LinkedBlockingQueue - Optionally bounded
3. PriorityBlockingQueue - Unbounded priority queue
4. DelayQueue - Elements with delay
5. SynchronousQueue - No capacity

```java
// ArrayBlockingQueue
BlockingQueue<String> boundedQueue = new ArrayBlockingQueue<>(10);

// Producer
boundedQueue.put("Item"); // Blocks if full
boundedQueue.offer("Item", 1, TimeUnit.SECONDS); // Timeout

// Consumer
String item = boundedQueue.take(); // Blocks if empty
String itemWithTimeout = boundedQueue.poll(1, TimeUnit.SECONDS);

// LinkedBlockingQueue (optionally bounded)
BlockingQueue<String> unboundedQueue = new LinkedBlockingQueue<>();
BlockingQueue<String> boundedQueue2 = new LinkedBlockingQueue<>(100);

// PriorityBlockingQueue
BlockingQueue<Integer> priorityQueue = new PriorityBlockingQueue<>();
priorityQueue.put(5);
priorityQueue.put(2);
priorityQueue.put(8);
Integer min = priorityQueue.take(); // 2 (minimum element)

// DelayQueue (elements with delay)
class DelayedTask implements Delayed {
    private String name;
    private long startTime;
    
    public DelayedTask(String name, long delayInMillis) {
        this.name = name;
        this.startTime = System.currentTimeMillis() + delayInMillis;
    }
    
    @Override
    public long getDelay(TimeUnit unit) {
        long diff = startTime - System.currentTimeMillis();
        return unit.convert(diff, TimeUnit.MILLISECONDS);
    }
    
    @Override
    public int compareTo(Delayed o) {
        return Long.compare(this.startTime, ((DelayedTask) o).startTime);
    }
}

DelayQueue<DelayedTask> delayQueue = new DelayQueue<>();
delayQueue.put(new DelayedTask("Task1", 1000));
DelayedTask task = delayQueue.take(); // Blocks until delay expires

// SynchronousQueue (handoff)
BlockingQueue<String> handoffQueue = new SynchronousQueue<>();
// Producer blocks until consumer is ready
// Consumer blocks until producer is ready
```

### BlockingDeque Interface

Double-ended queue with blocking operations.

```java
BlockingDeque<String> deque = new LinkedBlockingDeque<>();

// Blocking operations at both ends
deque.putFirst("First");
deque.putLast("Last");

String first = deque.takeFirst();
String last = deque.takeLast();

// Timed operations
boolean offered = deque.offerFirst("Item", 1, TimeUnit.SECONDS);
String polled = deque.pollLast(1, TimeUnit.SECONDS);
```

### TransferQueue Interface

BlockingQueue where producers can wait for consumers.

```java
TransferQueue<String> transferQueue = new LinkedTransferQueue<>();

// Producer thread
new Thread(() -> {
    try {
        transferQueue.transfer("Item"); // Blocks until consumed
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}).start();

// Consumer thread
new Thread(() -> {
    try {
        String item = transferQueue.take();
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}).start();

// Check if consumer is waiting
boolean hasWaitingConsumer = transferQueue.hasWaitingConsumer();
```

---

## Best Practices

### 1. Use Interfaces for Declaration

```java
// Good
List<String> list = new ArrayList<>();
Set<Integer> set = new HashSet<>();
Map<String, Object> map = new HashMap<>();

// Avoid
ArrayList<String> list = new ArrayList<>();
HashSet<Integer> set = new HashSet<>();
HashMap<String, Object> map = new HashMap<>();
```

**Benefits:**
- Flexibility to change implementation
- Better API design
- Easier testing

### 2. Choose Appropriate Collection

```java
// Fast random access
List<String> arrayList = new ArrayList<>();

// Frequent insertions/deletions at beginning
List<String> linkedList = new LinkedList<>();

// Unique elements, fast lookups
Set<String> hashSet = new HashSet<>();

// Unique elements, sorted order
Set<String> treeSet = new TreeSet<>();

// Key-value pairs, fast lookups
Map<String, Object> hashMap = new HashMap<>();

// Key-value pairs, sorted keys
Map<String, Object> treeMap = new TreeMap<>();

// FIFO queue
Queue<String> queue = new ArrayDeque<>();

// Priority queue
Queue<Integer> priorityQueue = new PriorityQueue<>();
```

### 3. Initialize with Capacity

```java
// Good - if you know approximate size
List<String> list = new ArrayList<>(1000);
Set<String> set = new HashSet<>(1000);
Map<String, Object> map = new HashMap<>(1000);

// Avoids multiple resizing operations
```

### 4. Use Immutable Collections (Java 9+)

```java
// Immutable collections
List<String> immutableList = List.of("A", "B", "C");
Set<String> immutableSet = Set.of("A", "B", "C");
Map<String, Integer> immutableMap = Map.of("A", 1, "B", 2);

// For more than 10 entries
Map<String, Integer> largeMap = Map.ofEntries(
    Map.entry("A", 1),
    Map.entry("B", 2),
    // ... more entries
);

// Benefits:
// - Thread-safe
// - Memory efficient
// - No accidental modifications
```

### 5. Use Diamond Operator

```java
// Good (Java 7+)
List<String> list = new ArrayList<>();
Map<String, List<Integer>> map = new HashMap<>();

// Avoid
List<String> list = new ArrayList<String>();
```

### 6. Avoid Raw Types

```java
// Bad - no type safety
List list = new ArrayList();
list.add("String");
list.add(123); // No compile error, runtime ClassCastException

// Good - type safe
List<String> list = new ArrayList<>();
// list.add(123); // Compile error
```

### 7. Use Enhanced For-Loop or Streams

```java
List<String> list = Arrays.asList("A", "B", "C");

// Enhanced for-loop
for (String item : list) {
    System.out.println(item);
}

// Stream (Java 8+)
list.stream()
    .filter(s -> s.startsWith("A"))
    .forEach(System.out::println);

// Avoid index-based iteration when not needed
for (int i = 0; i < list.size(); i++) {
    System.out.println(list.get(i));
}
```

### 8. Remove Elements Properly

```java
List<String> list = new ArrayList<>(Arrays.asList("A", "B", "C", "D"));

// Good - using Iterator
Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
    String item = iterator.next();
    if (item.equals("B")) {
        iterator.remove();
    }
}

// Good - using removeIf (Java 8+)
list.removeIf(item -> item.equals("B"));

// Bad - ConcurrentModificationException
for (String item : list) {
    if (item.equals("B")) {
        list.remove(item); // Throws exception
    }
}
```

### 9. Override equals() and hashCode()

```java
class Person {
    private String name;
    private int age;
    
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Person person = (Person) o;
        return age == person.age && Objects.equals(name, person.name);
    }
    
    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }
}

// Important for HashSet, HashMap, etc.
Set<Person> people = new HashSet<>();
people.add(new Person("Alice", 25));
people.contains(new Person("Alice", 25)); // true only if equals/hashCode implemented
```

### 10. Use try-with-resources for Streams

```java
// Good
try (Stream<String> lines = Files.lines(Paths.get("file.txt"))) {
    lines.forEach(System.out::println);
}

// Ensures stream is closed properly
```

### 11. Prefer EnumSet and EnumMap

```java
enum Status { ACTIVE, INACTIVE, PENDING }

// Prefer EnumSet over HashSet for enums
Set<Status> statusSet = EnumSet.of(Status.ACTIVE, Status.INACTIVE);

// Prefer EnumMap over HashMap for enum keys
Map<Status, String> statusMap = new EnumMap<>(Status.class);
```

### 12. Use Concurrent Collections Appropriately

```java
// For read-heavy scenarios
List<String> list = new CopyOnWriteArrayList<>();

// For concurrent HashMap
Map<String, Object> map = new ConcurrentHashMap<>();

// For producer-consumer
BlockingQueue<Task> queue = new LinkedBlockingQueue<>();

// Don't use Collections.synchronizedXxx unless needed
```

### 13. Null Handling

```java
// Use Optional (Java 8+)
Optional<String> optional = Optional.ofNullable(getValue());
String result = optional.orElse("default");

// Check for null in maps
Map<String, String> map = new HashMap<>();
String value = map.getOrDefault("key", "default");

// Some collections don't allow null
// TreeSet, TreeMap - NullPointerException
// ConcurrentHashMap - NullPointerException
```

### 14. Collection Conversion

```java
// List to Set
Set<String> set = new HashSet<>(list);

// Set to List
List<String> list = new ArrayList<>(set);

// Array to List
String[] array = {"A", "B", "C"};
List<String> list = Arrays.asList(array); // Fixed-size
List<String> mutableList = new ArrayList<>(Arrays.asList(array));

// Collection to Array
String[] array = list.toArray(new String[0]);
```

### 15. Performance Considerations

```java
// ArrayList vs LinkedList
// ArrayList: Fast random access O(1), slow insert/delete O(n)
// LinkedList: Slow random access O(n), fast insert/delete O(1)

// HashSet vs TreeSet
// HashSet: O(1) operations, no order
// TreeSet: O(log n) operations, sorted order

// HashMap vs TreeMap
// HashMap: O(1) operations, no order
// TreeMap: O(log n) operations, sorted keys

// Choose based on use case
```

---

## Performance Comparison

### Time Complexity Summary

| Collection | Add | Remove | Get | Contains | Iteration |
|-----------|-----|--------|-----|----------|-----------|
| ArrayList | O(1)* | O(n) | O(1) | O(n) | O(n) |
| LinkedList | O(1) | O(1)** | O(n) | O(n) | O(n) |
| HashSet | O(1)* | O(1)* | N/A | O(1)* | O(h/n)*** |
| LinkedHashSet | O(1)* | O(1)* | N/A | O(1)* | O(n) |
| TreeSet | O(log n) | O(log n) | N/A | O(log n) | O(n) |
| HashMap | O(1)* | O(1)* | O(1)* | O(1)* | O(h/n)*** |
| LinkedHashMap | O(1)* | O(1)* | O(1)* | O(1)* | O(n) |
| TreeMap | O(log n) | O(log n) | O(log n) | O(log n) | O(n) |
| PriorityQueue | O(log n) | O(log n) | O(1)**** | O(n) | O(n) |
| ArrayDeque | O(1)* | O(1)* | O(1) | O(n) | O(n) |
| ConcurrentHashMap | O(1)* | O(1)* | O(1)* | O(1)* | O(n) |

\* Amortized time  
\*\* At head/tail; O(n) at arbitrary position  
\*\*\* h is the table capacity  
\*\*\*\* Only for peek/element

### Space Complexity

| Collection | Space Complexity | Notes |
|-----------|------------------|-------|
| ArrayList | O(n) | Wastes space if not full |
| LinkedList | O(n) | Extra space for node references |
| HashSet | O(n) | Additional space for hash table |
| TreeSet | O(n) | Additional space for tree structure |
| HashMap | O(n) | Additional space for hash table |
| TreeMap | O(n) | Additional space for tree structure |

### When to Use Each Collection

**ArrayList:**
- Need fast random access
- Frequent iteration
- Infrequent insertions/deletions
- Know approximate size

**LinkedList:**
- Frequent insertions/deletions at beginning/end
- Implementing queue/deque
- Don't need random access

**HashSet:**
- Need unique elements
- Fast lookup required
- Order doesn't matter

**LinkedHashSet:**
- Need unique elements
- Maintain insertion order
- Fast lookup required

**TreeSet:**
- Need unique elements
- Maintain sorted order
- Need range operations

**HashMap:**
- Key-value pairs
- Fast lookup by key
- Order doesn't matter

**LinkedHashMap:**
- Key-value pairs
- Maintain insertion/access order
- Fast lookup by key

**TreeMap:**
- Key-value pairs
- Sorted by keys
- Need range operations

**PriorityQueue:**
- Need min/max element
- Priority-based processing
- Heap operations

**ArrayDeque:**
- Stack operations
- Queue operations
- Deque operations
- Better than Stack/LinkedList

**ConcurrentHashMap:**
- Thread-safe map
- High concurrency
- Better than Hashtable

### Benchmark Example

```java
import java.util.*;

public class CollectionBenchmark {
    
    public static void main(String[] args) {
        int size = 100000;
        
        // ArrayList vs LinkedList - Add
        List<Integer> arrayList = new ArrayList<>();
        List<Integer> linkedList = new LinkedList<>();
        
        long start = System.nanoTime();
        for (int i = 0; i < size; i++) {
            arrayList.add(i);
        }
        long arrayListAddTime = System.nanoTime() - start;
        
        start = System.nanoTime();
        for (int i = 0; i < size; i++) {
            linkedList.add(i);
        }
        long linkedListAddTime = System.nanoTime() - start;
        
        System.out.println("ArrayList add: " + arrayListAddTime / 1_000_000 + " ms");
        System.out.println("LinkedList add: " + linkedListAddTime / 1_000_000 + " ms");
        
        // HashSet vs TreeSet - Contains
        Set<Integer> hashSet = new HashSet<>();
        Set<Integer> treeSet = new TreeSet<>();
        
        for (int i = 0; i < size; i++) {
            hashSet.add(i);
            treeSet.add(i);
        }
        
        start = System.nanoTime();
        for (int i = 0; i < size; i++) {
            hashSet.contains(i);
        }
        long hashSetContainsTime = System.nanoTime() - start;
        
        start = System.nanoTime();
        for (int i = 0; i < size; i++) {
            treeSet.contains(i);
        }
        long treeSetContainsTime = System.nanoTime() - start;
        
        System.out.println("HashSet contains: " + hashSetContainsTime / 1_000_000 + " ms");
        System.out.println("TreeSet contains: " + treeSetContainsTime / 1_000_000 + " ms");
    }
}
```

---

## Summary

The Java Collection Framework provides:

1. **Interfaces**: Collection, List, Set, Queue, Deque, Map
2. **Implementations**: 
   - Lists: ArrayList, LinkedList, Vector, Stack
   - Sets: HashSet, LinkedHashSet, TreeSet, EnumSet
   - Queues: PriorityQueue, ArrayDeque
   - Maps: HashMap, LinkedHashMap, TreeMap, Hashtable, WeakHashMap, IdentityHashMap, EnumMap
   - Concurrent: ConcurrentHashMap, CopyOnWriteArrayList, BlockingQueue implementations
3. **Utilities**: Collections, Arrays
4. **Iterators**: Iterator, ListIterator, Enumeration
5. **Comparators**: Comparable, Comparator

**Key Takeaways:**
- Choose the right collection for your use case
- Use interfaces for declarations
- Initialize with appropriate capacity
- Override equals() and hashCode() when needed
- Use concurrent collections for thread-safety
- Prefer immutable collections when possible
- Understand time/space complexity trade-offs

---

**End of Guide**

For more information, refer to:
- [Java Documentation](https://docs.oracle.com/en/java/)
- [Java Collections API](https://docs.oracle.com/javase/8/docs/api/java/util/package-summary.html)
- [Effective Java by Joshua Bloch](https://www.oreilly.com/library/view/effective-java/9780134686097/)
