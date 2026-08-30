# Unordered Map Rehashing & Load Factor Mechanics (Step 1.6 — Learn Basic Hashing)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Unordered Map Rehashing & Load Factor Mechanics](https://takeuforward.org/c/unordered-map-rehashing/)
- **Difficulty**: Easy
- **Statement**: Explain how `std::unordered_map` manages its Load Factor ($\alpha = \text{size} / \text{bucket\_count}$), triggers rehashing when $\alpha > \text{max\_load\_factor}()$ (default 1.0), doubles bucket array, and how to use `reserve()` to prevent rehash latency spikes.

---

## 1. Problem, Restated

Understand dynamic hash table resizing, rehashing cost amortization, and reserve optimization.

- **Input**: Parameters specified.
- **Output**: Value or side-effect meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

When elements $N$ exceed buckets $M$ such that $\alpha > 1.0$, the average bucket chain length grows, degrading lookups to $\mathcal{O}(N)$. To restore $\mathcal{O}(1)$ performance, the table automatically allocates a new bucket array of $\approx 2M$ prime size, re-hashes every existing element into the new buckets, and deallocates the old table. Calling `map.reserve(N)` beforehand allocates $N$ capacity upfront, eliminating all runtime rehashing.

- **Underlying Pattern**: `Amortized Analysis & Dynamic Table Doubling`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Inserting $10^6$ elements without `reserve()`: triggers $\approx 20$ rehash cycles with multiple buffer copies.

### C++17 Code
```cpp
#include <unordered_map>
using namespace std;
void insertWithoutReserve(int n) {
    unordered_map<int, int> mp;
    for (int i = 0; i < n; i++) mp[i] = i; // Multiple rehash delays!
}
```

### Java Code
```java
import java.util.*;

class Solution {
    void insertWithoutReserve(int n) {
        Map<Integer, Integer> mp = new HashMap<>();
        for (int i = 0; i < n; i++) mp[i] = i; // Multiple rehash delays!
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ amortized, but suffers latency spikes during rehash passes.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Dynamic reallocation passes.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Use `mp.reserve(N)` to allocate buckets upfront in $\mathcal{O}(1)$ amortized time with zero rehashes.

### C++17 Code
```cpp
#include <iostream>
#include <unordered_map>
using namespace std;

int main() {
    unordered_map<int, int> mp;
    
    cout << "Initial bucket count: " << mp.bucket_count() << "\n";
    cout << "Max load factor: " << mp.max_load_factor() << "\n";
    
    // Reserve upfront for 100,000 elements (Zero rehashing during inserts)
    mp.reserve(100000);
    cout << "Bucket count after reserve(100k): " << mp.bucket_count() << "\n";
    
    for (int i = 0; i < 100000; i++) {
        mp[i] = i * 2;
    }
    
    cout << "Current load factor: " << mp.load_factor() << "\n";
    return 0;
}
```

### Java Code
```java
import java.util.*;

class Solution {
    int main() {
        Map<Integer, Integer> mp = new HashMap<>();
        
        System.out.print("Initial bucket count: " << mp.bucket_count() << "\n");
        System.out.print("Max load factor: " << mp.max_load_factor() << "\n");
        
        // Reserve upfront for 100,000 elements (Zero rehashing during inserts)
        mp.reserve(100000);
        System.out.print("Bucket count after public reserve(100k) { /* initialized: " << mp.bucket_count() << "\n");
        
        for (int i = 0; i < 100000; i++)  */ 
            mp[i] = i * 2;
         }
        
        System.out.print("Current load factor: " << mp.load_factor() << "\n");
        return 0;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ strict time per insertion with zero rehash passes.
- **Space Complexity**: $\mathcal{O}(N)$ memory.
- **Why this is optimal**: Single bucket allocation prevents heap fragmentation and re-insert passes.

---

## 6. Dry Run

Rehashing Trigger Progression ($M = 4$, max_load_factor = 1.0)

| Step | Action / State Change | Result |
|---|---|---|
| `N = 1..4` | $\alpha \le 4/4 = 1.0$ | Normal insertion into 4 buckets |
| `Insert 5th element` | $\alpha = 5/4 = 1.25 > 1.0$ (Rehash Triggered!) | New table allocated with $M = 8$ (or prime 11) |
| `Re-insert` | Re-compute $k \% 11$ for all 5 elements | Load factor resets to $\alpha = 5/11 \approx 0.45$ ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Bulk insertion of millions of elements in real-time latency-critical applications (e.g. trading engines).

### Common Bugs to Avoid
- Assuming `reserve(N)` resizes the map like `vector.resize()` (`reserve` only allocates buckets; size remains 0).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is rehashing O(N) but insertion is still O(1) amortized?**  
  **A**: By table doubling, an $\mathcal{O}(N)$ rehash only occurs after $N/2$ insertions have occurred since the last rehash. Distributing the $\mathcal{O}(N)$ rehash cost across the $N/2$ operations adds only $\mathcal{O}(1)$ amortized cost per insertion (Aggregate Analysis).


---

## 9. Tags & Related Problems

- **Tags**: `Hashing`, `Rehashing`, `Load Factor`, `Easy`
- **Related problems to practice next**:
- **Hash Map vs Hash Set**: Hash table benchmarks.
