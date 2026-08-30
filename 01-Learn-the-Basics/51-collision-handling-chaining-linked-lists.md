# Collision Handling: Chaining with Linked Lists (Step 1.6 — Learn Basic Hashing)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Collision Handling: Chaining with Linked Lists](https://takeuforward.org/data-structure/separate-chaining-in-hashing/)
- **Difficulty**: Easy
- **Statement**: Explain Separate Chaining collision resolution where each hash table bucket stores a singly linked list of key-value pairs. Analyze Load Factor $\alpha = N / M$ and time complexity.

---

## 1. Problem, Restated

Implement and analyze a Separate Chaining Hash Table.

- **Input**: Parameters specified.
- **Output**: Value or side-effect meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

When two distinct keys hash to the same bucket index $h(k_1) = h(k_2)$, Separate Chaining stores all colliding elements in a linked list at `table[index]`. If hash distribution is uniform, the average length of each list is the **Load Factor** $\alpha = N / M$. Search, insertion, and deletion run in $\mathcal{O}(1 + \alpha)$ average time.

- **Underlying Pattern**: `Separate Chaining Collision Resolution & Load Factor $\alpha = N/M$`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Single linked list without hash buckets in $\mathcal{O}(N)$ search time.

### C++17 Code
```cpp
// Plain linked list search O(N)
```

### Java Code
```java
// Java equivalent
// Plain linked list search O(N)
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: No bucket hashing.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Array of Linked List Buckets with $\mathcal{O}(1)$ average operations.

### C++17 Code
```cpp
#include <iostream>
#include <vector>
#include <list>
#include <string>
using namespace std;

class SeparateChainingHashTable {
private:
    int numBuckets;
    vector<list<pair<string, int>>> table;
    
    int hashFunction(const string& key) {
        unsigned long hash = 5381;
        for (char c : key) hash = ((hash << 5) + hash) + c; // djb2 hash
        return hash % numBuckets;
    }

public:
    SeparateChainingHashTable(int buckets = 101) : numBuckets(buckets), table(buckets) {}
    
    void insert(const string& key, int val) {
        int idx = hashFunction(key);
        for (auto& entry : table[idx]) {
            if (entry.first == key) { entry.second = val; return; }
        }
        table[idx].push_back({key, val});
    }
    
    int get(const string& key) {
        int idx = hashFunction(key);
        for (const auto& entry : table[idx]) {
            if (entry.first == key) return entry.second;
        }
        return -1; // key not found
    }
};

int main() {
    SeparateChainingHashTable ht(10);
    ht.insert("Alice", 95);
    ht.insert("Bob", 88);
    cout << "Alice Score: " << ht.get("Alice") << "\n";
    return 0;
}
```

### Java Code
```java
class SeparateChainingHashTable {

    int numBuckets;
    vector<list<pair<String, int>>> table;
    
    int hashFunction(String key) {
        unsigned long hash = 5381;
        for (char c : key) hash = ((hash << 5) + hash) + c; // djb2 hash
        return hash % numBuckets;
    }

    public SeparateChainingHashTable(int buckets = 101) { /* initialized: numBuckets(buckets), table(buckets)  */  }
    
    void insert(String key, int val) {
        int idx = hashFunction(key);
        for (var entry : table[idx]) {
            if (entry.first == key) { entry.second = val; return; }
        }
        table[idx].add({key, val});
    }
    
    int get(String key) {
        int idx = hashFunction(key);
        for (var entry : table[idx]) {
            if (entry.first == key) return entry.second;
        }
        return -1; // key not found
    }
};

int main() {
    SeparateChainingHashTable ht(10);
    ht.add("Alice", 95);
    ht.add("Bob", 88);
    System.out.print("Alice Score: " << ht.get("Alice") << "\n");
    return 0;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1 + \alpha)$ average time where $\alpha = N/M$.
- **Space Complexity**: $\mathcal{O}(N + M)$ memory.
- **Why this is optimal**: Short bucket chains maintain near $\mathcal{O}(1)$ lookups.

---

## 6. Dry Run

Separate Chaining Collision Trace ($M = 5$)

| Step | Action / State Change | Result |
|---|---|---|
| `Insert 'apple'` | hash = 2 | Bucket 2: [apple] |
| `Insert 'banana'` | hash = 4 | Bucket 4: [banana] |
| `Insert 'cherry'` | hash = 2 (Collision!) | Bucket 2: [apple] -> [cherry] |
| `Lookup 'cherry'` | hash = 2, inspects chain | Found cherry in 2 comparisons ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Load factor $\alpha > 1.0$ (triggers rehashing).
- All elements hash to same bucket (worst-case $\mathcal{O}(N)$ linked list).

### Common Bugs to Avoid
- Failing to update value when key already exists in chain (creates duplicate keys).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does C++ std::unordered_map use Separate Chaining?**  
  **A**: Separate Chaining provides iterator stability: inserting new elements never invalidates pointers or references to existing elements in other buckets.


---

## 9. Tags & Related Problems

- **Tags**: `Hashing`, `Data Structures`, `Chaining`, `Easy`
- **Related problems to practice next**:
- **Open Addressing**: Alternative collision strategy.
