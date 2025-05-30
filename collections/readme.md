**What is Collection Framework?**

* It is a set of classes and interfaces which provides a standard way to store, retrieve, and manipulate groups of individual objects efficiently.
* It is a part of the `java.util` package.

**Explain the Classification of Collection Framework?**

The Java Collection Framework is mainly divided into two types:

1. **Collection Interface**: Used to store groups of individual objects.
2. **Map Interface**: Used to store key-value pairs.

* Collection Interface has: List, Set, Queue
* Map Interface has: HashMap, SortedMap, HashTable

**What is Collection?**

* It is an interface used to represent a group of individual objects as a single entity.
* It provides common methods needed by its implementation types.
* It doesn't have concrete implementations; instead, it is implemented by interfaces like List, Set, and Queue.

**What are Methods Provided by Collection?**

* `add(Object obj)` – Adds the element
* `addAll(Collection c)` – Adds all elements in the given collection
* `remove(Object obj)` – Removes the given element
* `removeAll(Collection c)` – Removes all elements in the given collection
* `retainAll(Collection c)` – Retains only the elements in the given collection
* `clear()` – Removes all the elements
* `contains(Object obj)` – Checks if the given object exists
* `containsAll(Collection c)` – Checks if all elements of the given collection exist
* `isEmpty()` – Checks if the collection is empty
* `size()` – Returns the number of elements
* `toArray()` – Converts collection into an array
* `iterator()` – Returns an iterator for the collection

**Explain Classification of Collection Interface**

It is divided into three types:

* **List** – Ordered, allows duplicates
* **Set** – Unordered, doesn't allow duplicate values
* **Queue** – Ordered, allows duplicates, follows FIFO principle

**Explain List Interface**

* It is a child interface of Collection.
* Represents a group of objects where order is maintained and duplicates are allowed.
* Implementations:

  * **ArrayList** – Based on array; best for faster access.
  * **LinkedList** – Based on doubly linked list; best for faster insertions and deletions.
  * **Vector** – Synchronized version of ArrayList; thread-safe.
  * **Stack** – Child class of Vector; follows LIFO; thread-safe.

**Explain Set Interface**

* Child interface of Collection.
* Represents a group of objects with no duplicates and optional insertion order.
* Implementations:

  * **HashSet** – Based on HashTable; no duplicates; insertion order not preserved.
  * **LinkedHashSet** – Based on HashTable + LinkedList; insertion order preserved.
  * **TreeSet** – Based on balanced tree; no duplicates; maintains sorted order; elements must be homogeneous.

**Explain Queue Interface**

* Child interface of Collection.
* Represents group of objects where elements follow FIFO order; insertion order is optional.
* Types:

  * **PriorityQueue** – Processes elements based on priority; no duplicates.
  * **BlockingQueue (I)** – Used in multithreading environments (producer-consumer); common implementations: ArrayBlockingQueue, LinkedBlockingQueue, PriorityBlockingQueue, DelayQueue
  * **ArrayDeque** – Double-ended queue; can add/remove from both ends; can be used as Stack or Queue.

**Explain Map Interface**

* Interface used to represent objects in key-value pairs.
* Keys must be unique; values can be duplicate.
* Implementations:

  * **HashMap** – Based on HashTable; insertion order not preserved.
  * **LinkedHashMap** – Based on HashTable + LinkedList; insertion order preserved.
  * **TreeMap** – Based on Red-Black Tree; maintains sorted key-value pairs.

**Explain Methods of Map Interface**

* `put(key, value)` – Adds a key-value pair
* `putAll(Map m)` – Adds all key-value pairs from given map
* `get(key)` – Returns value of associated key
* `remove(key)` – Removes the key-value pair
* `containsKey(key)` – Checks if the key exists
* `containsValue(value)` – Checks if the value exists
* `isEmpty()` – Checks if map is empty
* `size()` – Returns the number of entries
* `clear()` – Removes all entries
* `keySet()` – Returns set of keys
* `values()` – Returns collection of values
* `entrySet()` – Returns set view of key-value pairs

**Explain NavigableSet and NavigableMap**

* Introduced in Java 1.7.
* Used for advanced navigation in sorted versions of Set and Map.
* `NavigableSet` extends `SortedSet`; `NavigableMap` extends `SortedMap`.
* Implemented by: `TreeSet`, `TreeMap`

**Explain About Cursors**

Used to traverse/iterate/retrieve objects one by one. Types of cursors in Java:

* **Enumeration** – Works for legacy collections like Vector, Stack. Provides only read access.
* **Iterator** – Works for all collections. Provides read and remove access. Moves in forward direction only.
* **ListIterator** – Works for List only. Provides read, remove, add, and replace access. Can move in both forward and backward directions.

**Explain Difference Between Comparable and Comparator**

* **Comparable**:

  * Defined in `java.lang` package.
  * Used for natural ordering of objects.
  * Class must implement `Comparable` and override `compareTo()` method.
  * Example: `public int compareTo(Object obj)`

* **Comparator**:

  * Defined in `java.util` package.
  * Used for customized sorting logic.
  * External class must implement `Comparator` and override `compare()` method.
  * Example: `public int compare(Object o1, Object o2)`
