-----------------
Comparator
-----------------
Comparator present in java.util package and it defines two methods compare() & equals().

1. public int compare(Object obj1, Object obj2)
   - Returns -ve if and only if obj1 has to come before obj2
   - Returns +ve if and only if obj1 has to come after obj2
   - Returns 0 if obj1 and obj2 are equal

2. public boolean equals(Object obj)

Whenever we are implementing the Comparator interface, we must provide an implementation for the compare method. However, we are not required to implement the equals method because it is already available to our class from the Object class through inheritance.

### Example: Sorting Integers in Descending Order using TreeSet

```java
import java.util.*;

class TreeSetDemo3 {
    public static void main(String[] args) {
        TreeSet<Integer> t = new TreeSet<>(new MyComparator());
        t.add(10);
        t.add(0);
        t.add(15);
        t.add(5);
        t.add(20);
        t.add(20);
        System.out.println(t);
    }
}

class MyComparator implements Comparator<Integer> {
    public int compare(Integer I1, Integer I2) {
        return I2.compareTo(I1);
    }
}
```

**Output:** `[20, 15, 10, 5, 0]`

### Example: Sorting Strings in Reverse Alphabetical Order using TreeSet

```java
import java.util.*;

class TreeSetStringDemo {
    public static void main(String[] args) {
        TreeSet<String> t = new TreeSet<>(new MyStringComparator());
        t.add("Roja");
        t.add("Shobharani");
        t.add("Rajakumari");
        t.add("GangaBhavani");
        t.add("Ramalamma");
        System.out.println(t);
    }
}

class MyStringComparator implements Comparator<String> {
    public int compare(String s1, String s2) {
        return s2.compareTo(s1);
    }
}
```

**Output:** `[Shobharani, Roja, Ramalamma, Rajakumari, GangaBhavani]`

### Comparison of Comparable and Comparator

| Property               | Comparable | Comparator |
|------------------------|------------|------------|
| Purpose               | Default natural sorting order | Custom sorting order |
| Package               | java.lang  | java.util  |
| Methods               | compareTo() | compare(), equals() |
| Built-in Implementation | String, Wrapper classes | Collator, RuleBasedCollator |

Using Comparator provides greater flexibility as we can define multiple sorting mechanisms as per our requirements.

