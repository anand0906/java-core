# **Queue in Java**

## **Introduction**
- Introduced in Java **1.5** as part of the **Collection Framework**.
- `Queue` is used to represent a **group of objects before processing**.
- Follows **First-In-First-Out (FIFO)** principle by default.
- **Can define custom priority orders** using `PriorityQueue`.
- `LinkedList` also implements `Queue`, always following FIFO order.

## **Hierarchy**
```
                Collection(I)(1.2)
                     |
        --------------------------------
        |               |              |
      List(I)        Set(I)       Queue(I)(1.5)
        |               |              |
  ----------------  ---------  -----------------
  |      |      |  |       |  |               |
  AL     LL  Vector  HS  LHS  PriorityQueue  BlockingQueue
```

---
## **Queue Interface Methods**

| Method | Description |
|--------|-------------|
| `boolean offer(Object o)` | Inserts an object into the queue. |
| `Object peek()` | Retrieves, but does not remove, the head of the queue. Returns `null` if empty. |
| `Object element()` | Retrieves the head but throws `NoSuchElementException` if the queue is empty. |
| `Object poll()` | Retrieves and removes the head. Returns `null` if empty. |
| `Object remove()` | Retrieves and removes the head but throws `NoSuchElementException` if empty. |

---
# **PriorityQueue in Java**

## **Introduction**
- Used for **processing elements based on priority**.
- **Order is not preserved**; elements are arranged based on priority.
- Supports **default natural ordering** or **custom comparator-defined ordering**.
- **Duplicate elements are not allowed**.
- **Does not allow `null` values**, even as the first element.

## **Constructors**
| Constructor | Description |
|------------|-------------|
| `PriorityQueue()` | Default initial capacity **11**, follows **natural sorting order**. |
| `PriorityQueue(int initialCapacity)` | Creates a priority queue with a custom capacity. |
| `PriorityQueue(int initialCapacity, Comparator c)` | Uses a **custom comparator** for sorting. |
| `PriorityQueue(SortedSet s)` | Creates a queue based on a `SortedSet`. |
| `PriorityQueue(Collection c)` | Initializes queue with elements from a collection. |

---
## **BlockingQueue in Java**

## **Introduction**
- `BlockingQueue` is an **interface** in `java.util.concurrent` package.
- Used in **multi-threading environments** where **producers and consumers** work asynchronously.
- It supports **blocking operations**, meaning:
  - If `take()` is called on an empty queue, it waits until an element is available.
  - If `put()` is called on a full queue, it waits until space is available.
- Does **not allow null elements**.
- Common implementations:
  - `ArrayBlockingQueue`
  - `LinkedBlockingQueue`
  - `PriorityBlockingQueue`
  - `DelayQueue`

## **Example: BlockingQueue in Action**
```java
import java.util.concurrent.*;

public class BlockingQueueExample {
    public static void main(String[] args) throws InterruptedException {
        BlockingQueue<Integer> queue = new ArrayBlockingQueue<>(3);
        
        queue.put(1);
        queue.put(2);
        queue.put(3);
        
        System.out.println(queue.take()); // Removes and returns 1
        System.out.println(queue.take()); // Removes and returns 2
        System.out.println(queue.take()); // Removes and returns 3
    }
}
```

### **Key Takeaways**
- `BlockingQueue` is **thread-safe**, making it ideal for **multi-threaded producer-consumer scenarios**.
- **Does not allow `null` values**.
- Blocks operations if the queue is **full (for `put()`)** or **empty (for `take()`)**.

---
## **Key Takeaways for Queue Implementations**
- **Use `Queue`** when **FIFO behavior is required**.
- **Use `PriorityQueue`** when elements **must be processed based on priority**.
- **Use `BlockingQueue`** for **multi-threaded environments** where blocking behavior is required.

# **Comparison of Queue Implementations in Java**

## **Overview**
- The `Queue` interface in Java represents a collection designed for **holding elements prior to processing**.
- Typically follows **First-In-First-Out (FIFO)** ordering.
- Different implementations provide **variations in behavior** such as priority ordering or blocking capabilities.

---
## **Comparison Table: Queue Implementations**

| Feature | LinkedList (Queue Mode) | PriorityQueue | BlockingQueue |
|---------|------------------------|--------------|--------------|
| **Ordering** | FIFO (First-In-First-Out) | Based on priority (natural/custom order) | FIFO or priority (depending on implementation) |
| **Null Allowed?** | ✅ Yes | ❌ No | ❌ No |
| **Thread-Safe?** | ❌ No | ❌ No | ✅ Yes (Thread-Safe) |
| **Blocking Operations?** | ❌ No | ❌ No | ✅ Yes (`put()` waits if full, `take()` waits if empty) |
| **Performance (Best Case)** | 🚀 O(1) for add/remove | ⚡ O(log n) for insert/remove | 🐢 Depends on implementation |
| **Duplicates Allowed?** | ✅ Yes | ✅ Yes | ✅ Yes |
| **Use Case** | Simple FIFO queue | Priority-based task execution | Multi-threaded producer-consumer scenarios |

---
## **Key Takeaways**
- **Use `LinkedList (Queue Mode)`** for **basic FIFO queue operations**.
- **Use `PriorityQueue`** when **elements must be processed based on priority**.
- **Use `BlockingQueue`** in **multi-threaded environments** where **blocking behavior is required**.

This guide provides a structured comparison of queue implementations, helping you choose the right one based on your needs. 🚀



