# Practice Questions – Day 2  
## Collections Framework

---

## Q1. You need to store a list of customer IDs where order of insertion must be preserved and duplicate IDs are allowed.

To store customer IDs while preserving insertion order and allowing duplicate values, the most suitable Java collection is an ArrayList. ArrayList maintains the order in which elements are added and permits duplicate entries, making it appropriate when the same customer ID can appear multiple times and sequence matters. If duplicates were not allowed, LinkedHashSet would be the correct choice because it preserves insertion order while automatically eliminating duplicate values.

```java
import java.util.ArrayList;
import java.util.LinkedHashSet;
import java.util.List;
import java.util.Set;

public class Q1CustomerIds {
    public static void main(String[] args) {
        List<Integer> customerIds = new ArrayList<>();
        customerIds.add(101);
        customerIds.add(102);
        customerIds.add(101);
        customerIds.add(103);
        System.out.println("With duplicates: " + customerIds);

        Set<Integer> uniqueCustomerIds = new LinkedHashSet<>();
        uniqueCustomerIds.add(101);
        uniqueCustomerIds.add(102);
        uniqueCustomerIds.add(101);
        uniqueCustomerIds.add(103);
        System.out.println("Without duplicates: " + uniqueCustomerIds);
    }
}
```

## Q2. In a multi-threaded application, multiple threads update a shared collection.

Collections such as ArrayList and HashMap are not thread-safe because they do not provide any internal synchronization. When multiple threads attempt to modify these collections at the same time, race conditions can occur, leading to inconsistent data or runtime exceptions. These collections are designed with the assumption that only one thread will access them at a time.

One thread-safe collection in Java is ConcurrentHashMap. It allows multiple threads to perform read and write operations concurrently by using fine-grained locking instead of locking the entire collection. Concurrent collections are preferred over Collections.synchronizedList() when high concurrency and performance are required, because synchronized collections lock the whole structure for each operation, which reduces scalability.
```
import java.util.concurrent.ConcurrentHashMap;

public class Q2ThreadSafeExample {
    public static void main(String[] args) {
        ConcurrentHashMap<Integer, String> map = new ConcurrentHashMap<>();
        map.put(1, "Alice");
        map.put(2, "Bob");
        System.out.println(map);
    }
}
```
## Q3. If an ArrayList is initialized with a size of 25 and a 26th element is added, what happens internally?

When an ArrayList is created with an initial capacity of 25, memory is allocated to store 25 elements. When the 26th element is added, the ArrayList exceeds its current capacity and automatically resizes itself. Internally, it creates a new array with a larger capacity, usually about 1.5 times the original size, copies all existing elements into the new array, and then adds the new element. This resizing operation is relatively expensive, which is why specifying an initial capacity is recommended when the expected size is known.

```
import java.util.ArrayList;

public class Q3ArrayListResize {
    public static void main(String[] args) {
        ArrayList<Integer> list = new ArrayList<>(25);
        for (int i = 1; i <= 26; i++) {
            list.add(i);
        }
        System.out.println("Final size: " + list.size());
    }
}
```

## Q4. Remove duplicate employee names where case should be treated as the same, while preserving insertion order.

In this problem, employee names may repeat and case differences should be ignored. The goal is to remove duplicates, preserve the original insertion order, and print only unique names. This can be achieved by storing normalized versions of names in a HashSet to detect duplicates and storing the first occurrence of each unique name in a list. This ensures that duplicates are removed in a case-insensitive manner while maintaining insertion order.

```
import java.util.*;

public class Q4EmployeeNames {
    public static void main(String[] args) {
        List<String> names = Arrays.asList(
                "John", "Alice", "john", "Bob", "Alice", "BOB"
        );

        Set<String> seen = new HashSet<>();
        List<String> result = new ArrayList<>();

        for (String name : names) {
            String normalized = name.toLowerCase();
            if (!seen.contains(normalized)) {
                seen.add(normalized);
                result.add(name);
            }
        }

        for (String name : result) {
            System.out.println(name);
        }
    }
}


Expected Output:

John
Alice
Bob
```
