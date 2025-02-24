## **1. Hierarchy of Java Collection Framework**

The Java Collection Framework is broadly classified into three major categories:

### **A. Collection Interface (Group of Objects)**
- **List** (Ordered, Allows Duplicates)
- **Set** (Unordered, No Duplicates)
- **Queue** (FIFO, Ordered)

### **B. Map Interface (Key-Value Pairs)**
- **SortedMap** (Keys in Sorted Order)
- **NavigableMap** (Enhanced Navigation Methods)

### **C. Iterator Interface (Traversing Collections)**

#### **Visual Representation of Collection Framework**
```
                Iterable (Root Interface)
                      |
                Collection (I)
             /      |         \ 
         List     Set      Queue
        /   |   /    \      |
ArrayList  LL HS   LHS  PriorityQueue
                \    |
              TreeSet NavigableSet
                      |
                   SortedSet
```
```
                Map (I)
                   |
          ----------------
          |              |
      SortedMap       HashMap
          |               |
     NavigableMap    LinkedHashMap
          |
      TreeMap
```

---
## **2. Key Interfaces in Java Collection Framework**

### **1. Collection (I)**
- Represents a group of individual objects as a single entity.
- Provides core methods like `add()`, `remove()`, `size()`, etc.
- Acts as the root interface for all collections.

> **Difference Between Collection and Collections**
> - **Collection**: Interface that represents a group of objects.
> - **Collections**: Utility class (`java.util.Collections`) that provides utility methods like sorting and searching.

---
### **2. List (I)**
- Ordered collection, allows duplicates.
- Preserves insertion order.
- Implementations:
  - **ArrayList** (Resizable array-based implementation)
  - **LinkedList** (Doubly linked list implementation)
  - **Vector** (Synchronized, thread-safe)
  - **Stack** (LIFO structure, extends Vector)

```
        List (I)
         |
  ------------------
  |       |       |
ArrayList  LL   Vector
                   |
                 Stack
```

---
### **3. Set (I)**
- Unordered collection, does not allow duplicates.
- Implementations:
  - **HashSet** (Uses hashing, unordered)
  - **LinkedHashSet** (Maintains insertion order)
  - **TreeSet** (Stores elements in sorted order)

```
        Set (I)
         |
  ------------------
  |               |
HashSet       SortedSet (I)
  |                 |
LinkedHashSet   NavigableSet (I)
                      |
                   TreeSet
```

---
### **4. Queue (I)**
- Follows FIFO (First-In-First-Out) order.
- Used for processing elements in order.
- Implementations:
  - **PriorityQueue** (Elements ordered by priority)
  - **BlockingQueue** (Thread-safe queues)

```
        Queue (I)
         |
  ------------------
  |               |
PriorityQueue  BlockingQueue
```

---
### **5. Map (I)**
- Stores key-value pairs.
- No duplicate keys allowed.
- Implementations:
  - **HashMap** (Unordered key-value storage)
  - **LinkedHashMap** (Maintains insertion order)
  - **TreeMap** (Stores keys in sorted order)
  - **WeakHashMap** (Garbage collection support)
  - **IdentityHashMap** (Uses reference equality for keys)

```
        Map (I)
         |
  ------------------
  |       |       |
HashMap  SortedMap  WeakHashMap
  |       |
LHM   NavigableMap
          |
       TreeMap
```

---
## **3. Comparison of Key Collection Types**

| Feature       | List | Set | Queue | Map |
|--------------|------|-----|-------|-----|
| Duplicates Allowed? | ✅ Yes | ❌ No | ✅ Yes | ❌ No (Keys) |
| Ordered? | ✅ Yes | ❌ No | ✅ Yes | ✅ Yes (LHM, TreeMap) |
| Sorting? | ❌ No | ✅ TreeSet | ✅ PriorityQueue | ✅ TreeMap |
| Key-Value Pair? | ❌ No | ❌ No | ❌ No | ✅ Yes |

---
## **4. Legacy Classes in Collection Framework**
- **Enumeration (I)** - Used in legacy classes like Vector.
- **Dictionary (AC)** - Abstract class, parent of Hashtable.
- **Vector (C)** - Thread-safe alternative to ArrayList.
- **Stack (C)** - Implements LIFO behavior.
- **Hashtable (C)** - Legacy synchronized Map implementation.
- **Properties (C)** - Used for key-value property files.

---
## **Conclusion**
- **Lists** are ordered and allow duplicates.
- **Sets** are unordered and do not allow duplicates.
- **Queues** are used for ordering elements for processing.
- **Maps** store key-value pairs.
- **Navigable and Sorted interfaces** provide ordering and navigation support.

This guide provides a complete overview of the **Java Collection Framework** with organized, structured, and visual content. 🚀 Let me know if you need further clarifications!

