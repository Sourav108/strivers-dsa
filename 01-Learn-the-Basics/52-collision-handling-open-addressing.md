# Collision Handling: Open Addressing (Linear & Quadratic Probing) (Step 1.6 — Learn Basic Hashing)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Collision Handling: Open Addressing (Linear & Quadratic Probing)](https://takeuforward.org/data-structure/open-addressing/)
- **Difficulty**: Easy
- **Statement**: Explain Open Addressing collision resolution methods: Linear Probing ($h(k, i) = (h'(k) + i) \% M$), Quadratic Probing ($h(k, i) = (h'(k) + c_1 i + c_2 i^2) \% M$), Double Hashing, and Tombstone deletion.

---

## 1. Problem, Restated

Understand probing sequences, primary/secondary clustering, and lazy deletion.

- **Input**: Parameters specified.
- **Output**: Value or side-effect meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

In Open Addressing, all elements are stored directly inside the hash table array (no linked lists). When collision occurs at $h(k)$, probe subsequent slots $h(k, 1), h(k, 2), \dots$ until an empty cell is found. Deletion requires placing a **Tombstone (`DELETED`)** marker to prevent breaking the probe chain for subsequent elements.

- **Underlying Pattern**: `Open Addressing & Probing Sequence Optimization`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Linear Probing: $h(k, i) = (h'(k) + i) \% M$ (prone to Primary Clustering).

### C++17 Code
```cpp
// Linear Probing: index = (hash + i) % M
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ average, degrades under high load factor.
- **Space Complexity**: $\mathcal{O}(1)$ per cell.
- **Why it's not good enough**: Clusters of occupied slots form long contiguous blocks.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Open Addressing with Tombstone Deletion and Quadratic Probing.

### C++17 Code
```cpp
#include <iostream>
#include <vector>
using namespace std;

enum CellState { EMPTY, OCCUPIED, DELETED };

struct Cell {
    int key;
    int val;
    CellState state = EMPTY;
};

class OpenAddressingHashTable {
private:
    int M;
    vector<Cell> table;
    
public:
    OpenAddressingHashTable(int size = 11) : M(size), table(size) {}
    
    bool insert(int key, int val) {
        int idx = key % M;
        for (int i = 0; i < M; i++) {
            int probe = (idx + i * i) % M; // Quadratic Probing: idx + i^2
            if (table[probe].state == EMPTY || table[probe].state == DELETED) {
                table[probe] = {key, val, OCCUPIED};
                return true;
            }
            if (table[probe].state == OCCUPIED && table[probe].key == key) {
                table[probe].val = val; // update existing key
                return true;
            }
        }
        return false; // Table full
    }
    
    int get(int key) {
        int idx = key % M;
        for (int i = 0; i < M; i++) {
            int probe = (idx + i * i) % M;
            if (table[probe].state == EMPTY) return -1; // stop on EMPTY
            if (table[probe].state == OCCUPIED && table[probe].key == key) {
                return table[probe].val;
            }
        }
        return -1;
    }
};

int main() {
    OpenAddressingHashTable ht(7);
    ht.insert(10, 100);
    ht.insert(17, 200); // 10 % 7 = 3, 17 % 7 = 3 (Collision -> probes 3+1^2=4)
    cout << "Key 17 Val: " << ht.get(17) << "\n";
    return 0;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\frac{1}{1 - \alpha})$ average probes (efficient when $\alpha < 0.7$).
- **Space Complexity**: $\mathcal{O}(M)$ contiguous array (zero pointer overhead, excellent cache locality).
- **Why this is optimal**: Flat array memory layout maximizes CPU cache hits.

---

## 6. Dry Run

Quadratic Probing Trace ($M = 7$)

| Step | Action / State Change | Result |
|---|---|---|
| `Insert 10` | $10 \% 7 = 3$ | Slot 3 occupied by 10 |
| `Insert 17` | $17 \% 7 = 3$ (Collision!) | Probe $i=1$: $(3 + 1^2) \% 7 = 4$. Slot 4 occupied by 17 |
| `Search 17` | Inspect slot 3 (val 10), probe slot 4 (val 17) | Found in 2 probes ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Load factor $\alpha \ge 0.7$ (must resize to prevent infinite loop).
- Tombstones filling table.

### Common Bugs to Avoid
- Stopping search when encountering a `DELETED` slot instead of continuing.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is the difference between Primary and Secondary Clustering?**  
  **A**: Primary clustering occurs in Linear Probing where occupied blocks merge into long contiguous runs. Secondary clustering occurs in Quadratic Probing where keys with identical initial hash values follow the same probe sequence.


---

## 9. Tags & Related Problems

- **Tags**: `Hashing`, `Data Structures`, `Open Addressing`, `Easy`
- **Related problems to practice next**:
- **Separate Chaining**: Chaining comparison.
