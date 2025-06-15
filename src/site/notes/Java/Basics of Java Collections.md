---
{"dg-publish":true,"dg-enable-search":true,"comments":true,"tags":["java","collections"],"permalink":"/java/basics-of-java-collections/","dgEnableSearch":true,"dgPassFrontmatter":true}
---

## **1. What is the Java Collections Framework?**

- The **Java Collections Framework (JCF)** is a set of classes and interfaces for **storing and managing groups of objects efficiently**.
- Collections provide **dynamic resizing**, unlike arrays, which have a fixed size.

### 1.1 How does Dynamic Resizing work?

#### **A. ArrayList (Dynamic Array)**

**How it works:**

- Internally uses a **resizable array (`Object[]`)**.
- **Initial size:** `10` (default).
- **When it resizes:** If adding an element exceeds capacity, it **doubles** the size (`oldCapacity * 1.5` in Java 8+).
- **Performance cost:** Resizing involves **creating a new array and copying elements** (`O(n)`).

##### **Example: How ArrayList Expands**

```java
import java.util.*;

public class ArrayListResizing {
    public static void main(String[] args) {
        List<Integer> list = new ArrayList<>(2);  // Initial capacity = 2

        list.add(10);  // ✅ No resizing
        list.add(20);  // ✅ No resizing
        list.add(30);  // 🚀 Triggers resizing (New capacity: 2 * 1.5 = 3)

        System.out.println("List: " + list);
    }
}
```

🚀 **Behind the scenes:**

```java
// Before resizing (capacity = 2)
[10, 20]

// After resizing (capacity = 3)
[10, 20, 30]
```

💡 **Best Practice:**  
If you know the expected number of elements, set an **initial capacity** to avoid excessive resizing:

```java
List<Integer> list = new ArrayList<>(1000);  // Avoids frequent resizing
```

---

#### **B. HashMap & HashSet (Resizing with Load Factor)**

**How it works:**

- Uses an **array of buckets** (linked lists in Java 7, trees in Java 8+ for collision handling).
- **Initial size:** `16` (default).
- **When it resizes:** When the **load factor (`0.75` default) is exceeded** → new size = `oldCapacity * 2`.
- **Performance cost:** All elements are **rehashed** (`O(n)`).

##### **Example: How HashMap Expands**

```java
import java.util.*;

public class HashMapResizing {
    public static void main(String[] args) {
        Map<Integer, String> map = new HashMap<>(2, 0.75f);  // Small capacity for demo

        map.put(1, "One");  // ✅ No resizing
        map.put(2, "Two");  // ✅ No resizing
        map.put(3, "Three");  // 🚀 Triggers resizing (new capacity = 4)

        System.out.println("Map: " + map);
    }
}
```

🚀 **Behind the scenes:**

```java
// Before resizing (capacity = 2, load factor = 0.75)
[1, 2]

// After resizing (capacity = 4)
[1, 2, 3]
```

💡 **Best Practice:**  
If storing many elements, set an **initial capacity**:

```java
Map<Integer, String> largeMap = new HashMap<>(1000);
```

---

#### **C. LinkedList (No Resizing Needed)**

- Uses **doubly linked nodes**, no resizing needed.
- **Each node** holds a reference to the **previous and next node**.
- **Slower access** than `ArrayList` but **fast insertions/removals**.

🚀 **When to use LinkedList?**

- Frequent insertions/deletions (middle of list).
- Large data where resizing overhead of `ArrayList` is costly.

---

#### **D. PriorityQueue (Heap-based Resizing)**

- Uses **binary heap** internally.
- **Resizes like ArrayList** but uses **heap property (min/max priority).**

```java
Queue<Integer> pq = new PriorityQueue<>();
pq.add(10);
pq.add(20);
pq.add(30);  // Automatically grows
```

🚀 **Used for:** Task scheduling, Dijkstra’s Algorithm, job priority queues.

---

> **Performance Implications of Resizing**
>
>| Collection        | Growth Strategy     | Performance Impact                       |
>| ----------------- | ------------------- | ---------------------------------------- |
>| **ArrayList**     | `1.5 * oldCapacity` | `O(n)` copy operation when resizing      |
>| **HashMap**       | `2 * oldCapacity`   | `O(n)` rehashing, reassigning buckets    |
>| **LinkedList**    | No resizing         | No resizing cost, but `O(n)` search time |
>| **PriorityQueue** | `2 * oldCapacity`   | Rebalancing after resize (`O(log n)`)    |

---

### **2. Why Use Collections Instead of Arrays?**

|Feature|Arrays|Collections|
|---|---|---|
|**Size**|Fixed|Dynamic|
|**Data Type**|Primitives & Objects|Objects only|
|**Performance**|Fast but limited|More flexible|
|**Utility Methods**|None (manual looping)|Sorting, Searching, Thread-safety|

Example:

```java
// Using Arrays
int[] numbers = new int[5];  // Fixed size

// Using Collections (ArrayList)
List<Integer> numberList = new ArrayList<>();  // Dynamic size
numberList.add(10);
numberList.add(20);
```

---

## **3. Core Interfaces in the Collections Framework**

Java Collections is built around **four main interfaces**:

- **List** → Ordered collection (allows duplicates)
- **Set** → Unordered, unique elements only
- **Queue** → Follows FIFO (First In, First Out)
- **Map** → Key-value pairs (keys must be unique)

### **Basic Example of Each**

```java
// List Example (Allows Duplicates)
List<String> names = new ArrayList<>();
names.add("Alice");
names.add("Bob");
names.add("Alice");  // Duplicates allowed

// Set Example (No Duplicates)
Set<String> uniqueNames = new HashSet<>();
uniqueNames.add("Alice");
uniqueNames.add("Bob");
uniqueNames.add("Alice");  // Ignored

// Queue Example (FIFO)
Queue<Integer> queue = new LinkedList<>();
queue.add(1);
queue.add(2);
System.out.println(queue.poll());  // Output: 1 (removes the first element)

// Map Example (Key-Value Store)
Map<String, Integer> ageMap = new HashMap<>();
ageMap.put("Alice", 25);
ageMap.put("Bob", 30);
System.out.println(ageMap.get("Alice"));  // Output: 25
```

---

## **4. Understanding Generics in Collections**

Collections use **generics** to ensure **type safety**.

Without Generics:

```java
List numbers = new ArrayList();  // Can store any Object (bad practice)
numbers.add("Hello");  // No type check
numbers.add(10);       // No type check
```

With Generics:

```java
List<Integer> numbers = new ArrayList<>();
numbers.add(10);  // Only Integers allowed
numbers.add("Hello");  // ❌ Compile-time error
```

🚀 **Why Use Generics?**

1. **Prevents runtime errors** (no accidental type mismatches)
2. **Eliminates type casting** (easier to work with objects)
3. **Improves code readability**

---

## **5. ArrayList vs. LinkedList (Basic Understanding)**

|Feature|ArrayList|LinkedList|
|---|---|---|
|**Implementation**|Uses a **dynamic array**|Uses a **doubly linked list**|
|**Access Time**|Fast (`O(1)`)|Slow (`O(n)`)|
|**Insertion/Deletion**|Slow (`O(n)`)|Fast (`O(1)`)|
|**Memory Usage**|Less (stores only values)|More (stores values + pointers)|

Example:

```java
List<Integer> arrayList = new ArrayList<>();
arrayList.add(10);  // Fast random access

List<Integer> linkedList = new LinkedList<>();
linkedList.add(10);  // Fast insertion/removal
```

---

## **6. When to Use Which Collection? (Basic)**

|Scenario|Use This|
|---|---|
|Need fast **random access**|`ArrayList`|
|Need fast **insert/delete operations**|`LinkedList`|
|Need a **unique set of elements**|`HashSet`|
|Need **key-value pairs**|`HashMap`|
|Need a **thread-safe collection**|`ConcurrentHashMap`|

---

## **🔥 Quick Coding Exercise (Basics)**

👉 **Try to answer before running the code!**

```java
import java.util.*;

public class CollectionBasics {
    public static void main(String[] args) {
        List<String> fruits = new ArrayList<>();
        fruits.add("Apple");
        fruits.add("Banana");
        fruits.add("Apple");  // What will happen?

        Set<String> uniqueFruits = new HashSet<>(fruits);
        System.out.println(uniqueFruits.size());  // Output?

        Map<String, Integer> scores = new HashMap<>();
        scores.put("Alice", 95);
        scores.put("Bob", 85);
        scores.put("Alice", 90);  // What happens to "Alice"'s score?
        
        System.out.println(scores.get("Alice"));  // Output?
    }
}
```

