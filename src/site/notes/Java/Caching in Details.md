---
{"dg-publish":true,"dg-show-toc":true,"comments":true,"tags":["cache","java"],"permalink":"/java/caching-in-details/","dgShowToc":true,"dgPassFrontmatter":true}
---

Caching is crucial in high-performance systems to minimise latency, prevent redundant processing, and enhance scalability**. Different use cases require different **caching strategies**, depending on factors such as data freshness, access patterns, and memory constraints.

---
## 🔁 Common Caching Strategies (with Pros & Use Cases)
---
I will start with an abstract class so that we can implement each cache. This approach helps in maintaining polymorphism and makes it easier to test each one of them.

```java
public interface Cache<K, V> {
    void put(K key, V value);
    V get(K key);
    void remove(K key);
    boolean containsKey(K key);
    int size();
}
```


### 1. **LRU (Least Recently Used)**

- **Evicts** the item that hasn’t been used for the longest time.
- ✅ Good for general-purpose caches (e.g., image thumbnails, database query results).

🧠 **Use When**: You assume recently accessed items will be used again soon.

➡️ **Java Example**: Use `LinkedHashMap` with access order:
```java
import java.util.*;

public class LRUCache<K, V> implements Cache<K, V> {
    private final int capacity;
    private final LinkedHashMap<K, V> map;

    public LRUCache(int capacity) {
        this.capacity = capacity;
        this.map = new LinkedHashMap<>(capacity, 0.75f, true) {
            protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
                return size() > LRUCache.this.capacity;
            }
        };
    }

    @Override
    public void put(K key, V value) {
        map.put(key, value);
    }

    @Override
    public V get(K key) {
        return map.get(key);
    }

    @Override
    public void remove(K key) {
        map.remove(key);
    }

    @Override
    public boolean containsKey(K key) {
        return map.containsKey(key);
    }

    @Override
    public int size() {
        return map.size();
    }
}

```

---
### 2. **LFU (Least Frequently Used)**

- **Evicts** items that are used the **least number of times**.    
- More complex to implement than LRU.
- ✅ Useful when **hot data stays hot** (like in recommendation systems or ad delivery).

🧠 **Use When**: Access frequency is more important than recency.

➡️ Requires:

- HashMap for key-value
- HashMap for key-count
- TreeMap or MinHeap to track frequency

```java
import java.util.*;

public class LFUCache<K, V> implements Cache<K, V> {
    private final int capacity;
    private int minFreq = 1;

    private final Map<K, V> valueMap = new HashMap<>();
    private final Map<K, Integer> freqMap = new HashMap<>();
    private final Map<Integer, LinkedHashSet<K>> freqListMap = new HashMap<>();

    public LFUCache(int capacity) {
        this.capacity = capacity;
    }

    @Override
    public void put(K key, V value) {
        if (capacity <= 0) return;

        if (valueMap.containsKey(key)) {
            valueMap.put(key, value);
            get(key); // increase frequency
            return;
        }

        if (valueMap.size() >= capacity) {
            K evictKey = freqListMap.get(minFreq).iterator().next();
            freqListMap.get(minFreq).remove(evictKey);
            valueMap.remove(evictKey);
            freqMap.remove(evictKey);
        }

        valueMap.put(key, value);
        freqMap.put(key, 1);
        freqListMap.computeIfAbsent(1, f -> new LinkedHashSet<>()).add(key);
        minFreq = 1;
    }

    @Override
    public V get(K key) {
        if (!valueMap.containsKey(key)) return null;

        int freq = freqMap.get(key);
        freqListMap.get(freq).remove(key);
        if (freqListMap.get(freq).isEmpty()) {
            freqListMap.remove(freq);
            if (minFreq == freq) minFreq++;
        }

        freqMap.put(key, freq + 1);
        freqListMap.computeIfAbsent(freq + 1, f -> new LinkedHashSet<>()).add(key);

        return valueMap.get(key);
    }

    @Override
    public void remove(K key) {
        if (!valueMap.containsKey(key)) return;

        int freq = freqMap.get(key);
        freqMap.remove(key);
        valueMap.remove(key);
        freqListMap.get(freq).remove(key);

        if (freqListMap.get(freq).isEmpty()) {
            freqListMap.remove(freq);
            if (minFreq == freq) minFreq++;
        }
    }

    @Override
    public boolean containsKey(K key) {
        return valueMap.containsKey(key);
    }

    @Override
    public int size() {
        return valueMap.size();
    }
}

```
---
### 3. **FIFO (First-In First-Out)**

- **Evicts** the item that was cached **first**, regardless of access pattern.
- ❌ Not ideal for general caching unless fairness is required.

🧠 **Use When**: You want predictable eviction timing (e.g., logs, queue-like behavior).

➡️ Simple with a `Queue` and `Map`.

```java
import java.util.*;

public class FIFOCache<K, V> implements Cache<K, V> {
    private final int capacity;
    private final Map<K, V> map = new LinkedHashMap<>();
    private final Queue<K> queue = new LinkedList<>();

    public FIFOCache(int capacity) {
        this.capacity = capacity;
    }

    @Override
    public void put(K key, V value) {
        if (!map.containsKey(key) && map.size() >= capacity) {
            K oldestKey = queue.poll();
            if (oldestKey != null) map.remove(oldestKey);
        }
        queue.add(key);
        map.put(key, value);
    }

    @Override
    public V get(K key) {
        return map.get(key);
    }

    @Override
    public void remove(K key) {
        queue.remove(key);
        map.remove(key);
    }

    @Override
    public boolean containsKey(K key) {
        return map.containsKey(key);
    }

    @Override
    public int size() {
        return map.size();
    }
}


```

---

### 4. **Time-based / TTL (Time to Live)**

- Cache items **expire** after a fixed duration.
- ✅ Great for scenarios where **freshness matters**, like API responses or tokens.

🧠 **Use When**: Stale data is unacceptable after a certain time.

➡️ Example:
```java
import java.util.*;

public class TimedCache<K, V> implements Cache<K, V> {
    private static class Entry<V> {
        V value;
        long expiry;
        Entry(V value, long expiry) {
            this.value = value;
            this.expiry = expiry;
        }
    }

    private final long ttlMillis;
    private final Map<K, Entry<V>> map = new HashMap<>();

    public TimedCache(long ttlMillis) {
        this.ttlMillis = ttlMillis;
    }

    @Override
    public void put(K key, V value) {
        long expiry = System.currentTimeMillis() + ttlMillis;
        map.put(key, new Entry<>(value, expiry));
    }

    @Override
    public V get(K key) {
        Entry<V> entry = map.get(key);
        if (entry == null || System.currentTimeMillis() > entry.expiry) {
            map.remove(key);
            return null;
        }
        return entry.value;
    }

    @Override
    public void remove(K key) {
        map.remove(key);
    }

    @Override
    public boolean containsKey(K key) {
        return get(key) != null;
    }

    @Override
    public int size() {
        return map.size();
    }
}

```

---

### 5. **Write-through Cache**

- Writes go to the **cache and the backing store (e.g., DB)** simultaneously.
- ✅ Ensures cache and source stay in sync.
- ❌ Slightly slower write performance.

🧠 **Use When**: You want strong consistency and no stale data.

```java
import java.util.*;

public class WriteThroughCache<K, V> implements Cache<K, V> {
    private final Map<K, V> cache = new HashMap<>();
    private final Map<K, V> backingStore;

    public WriteThroughCache(Map<K, V> backingStore) {
        this.backingStore = backingStore;
    }

    @Override
    public void put(K key, V value) {
        cache.put(key, value);
        backingStore.put(key, value); // immediate sync
    }

    @Override
    public V get(K key) {
        return cache.getOrDefault(key, backingStore.get(key));
    }

    @Override
    public void remove(K key) {
        cache.remove(key);
        backingStore.remove(key);
    }

    @Override
    public boolean containsKey(K key) {
        return cache.containsKey(key) || backingStore.containsKey(key);
    }

    @Override
    public int size() {
        return cache.size();
    }
}

```

---
### 6. **Write-behind Cache (Write-back)**

- Writes go to **cache immediately**, and the **backing store is updated later (asynchronously)**.  
- ✅ Faster writes, good for bulk updates.
- ❌ Data may be lost if the system crashes before flushing.

🧠 **Use When**: You can tolerate temporary inconsistency for speed.

```java
import java.util.*;
import java.util.concurrent.*;

public class WriteBehindCache<K, V> implements Cache<K, V> {
    private final Map<K, V> cache = new ConcurrentHashMap<>();
    private final Map<K, V> backingStore = new ConcurrentHashMap<>();
    private final BlockingQueue<K> queue = new LinkedBlockingQueue<>();

    public WriteBehindCache() {
        Thread worker = new Thread(() -> {
            while (true) {
                try {
                    K key = queue.take();
                    V value = cache.get(key);
                    backingStore.put(key, value); // simulate DB flush
                } catch (InterruptedException ignored) {}
            }
        });
        worker.setDaemon(true);
        worker.start();
    }

    @Override
    public void put(K key, V value) {
        cache.put(key, value);
        queue.offer(key);
    }

    @Override
    public V get(K key) {
        return cache.getOrDefault(key, backingStore.get(key));
    }

    @Override
    public void remove(K key) {
        cache.remove(key);
        backingStore.remove(key);
    }

    @Override
    public boolean containsKey(K key) {
        return cache.containsKey(key) || backingStore.containsKey(key);
    }

    @Override
    public int size() {
        return cache.size();
    }
}
```

---
### 7. **Read-through Cache**

- The application only talks to the **cache**, which **fetches from the source** if data is missing.
- ✅ Simplifies logic, consistent reads.

🧠 **Use When**: You want a smart cache that auto-loads on miss.

```java
import java.util.*;
import java.util.function.Function;

public class ReadThroughCache<K, V> implements Cache<K, V> {
    private final Map<K, V> cache = new HashMap<>();
    private final Function<K, V> loader;

    public ReadThroughCache(Function<K, V> loader) {
        this.loader = loader;
    }

    @Override
    public void put(K key, V value) {
        cache.put(key, value);
    }

    @Override
    public V get(K key) {
        return cache.computeIfAbsent(key, loader);
    }

    @Override
    public void remove(K key) {
        cache.remove(key);
    }

    @Override
    public boolean containsKey(K key) {
        return cache.containsKey(key);
    }

    @Override
    public int size() {
        return cache.size();
    }
}

```

---
### 8. **Refresh-ahead / Auto-refresh Cache**

- Cache preloads or refreshes items **before they expire**, keeping them warm.
- ✅ Useful when you know queries will hit soon again.

🧠 **Use When**: You want to avoid sudden cache misses on expiry.

```java
import java.util.*;
import java.util.concurrent.*;
import java.util.function.Function;

public class RefreshAheadCache<K, V> implements Cache<K, V> {
    private static class Entry<V> {
        V value;
        long expiry;
        Entry(V value, long expiry) {
            this.value = value;
            this.expiry = expiry;
        }
    }

    private final Map<K, Entry<V>> cache = new ConcurrentHashMap<>();
    private final long ttlMillis;
    private final Function<K, V> loader;

    public RefreshAheadCache(long ttlMillis, Function<K, V> loader) {
        this.ttlMillis = ttlMillis;
        this.loader = loader;

        Executors.newSingleThreadScheduledExecutor().scheduleAtFixedRate(this::refresh,
                1, 1, TimeUnit.SECONDS);
    }

    @Override
    public void put(K key, V value) {
        cache.put(key, new Entry<>(value, System.currentTimeMillis() + ttlMillis));
    }

    @Override
    public V get(K key) {
        Entry<V> entry = cache.get(key);
        if (entry == null || System.currentTimeMillis() > entry.expiry) return null;
        return entry.value;
    }

    private void refresh() {
        long now = System.currentTimeMillis();
        for (K key : cache.keySet()) {
            Entry<V> entry = cache.get(key);
            if (entry != null && entry.expiry - now < ttlMillis / 4) {
                V newValue = loader.apply(key);
                cache.put(key, new Entry<>(newValue, now + ttlMillis));
            }
        }
    }

    @Override
    public void remove(K key) {
        cache.remove(key);
    }

    @Override
    public boolean containsKey(K key) {
        return get(key) != null;
    }

    @Override
    public int size() {
        return cache.size();
    }
}

```

---
## 🧠 Choosing the Right Strategy

| Use Case                                  | Suggested Strategy   |
| ----------------------------------------- | -------------------- |
| Frequently accessed recent data           | **LRU**              |
| Popular items accessed repeatedly         | **LFU**              |
| Stale data is unacceptable                | **TTL / Time-based** |
| Need strong consistency                   | **Write-through**    |
| High-speed writes are more important      | **Write-behind**     |
| Want cache to handle misses automatically | **Read-through**     |
| Want proactive updates                    | **Refresh-ahead**    |
	