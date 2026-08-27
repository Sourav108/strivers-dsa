# Counting Frequencies of Array Elements (Step 1.6 — Learn Basic Hashing)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Counting Frequencies of Array Elements](https://takeuforward.org/data-structure/count-frequency-of-each-element-in-the-array/)
- **Difficulty**: Easy
- **Statement**: Given an array `arr` of $n$ integers, count the frequency of each element and print or return the counts.

---

## 1. Problem, Restated

Build a frequency map mapping each distinct element to its occurrence count in $\mathcal{O}(N)$ time.

- **Input**: Parameters specified.
- **Output**: Value or side-effect meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Instead of running nested $\mathcal{O}(N^2)$ loops to count occurrences, use a hash map `unordered_map<int, int>` to record frequencies in a single pass in $\mathcal{O}(N)$ average time. If values are bounded ($1 \le \text{arr}[i] \le M$), a direct frequency array `int hash[M+1]` gives guaranteed $\mathcal{O}(1)$ lookups.

- **Underlying Pattern**: `Frequency Hash Map / Direct Array Hashing`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Nested loops checking each element against visited array in $\mathcal{O}(N^2)$.

### C++17 Code
```cpp
#include <vector>
using namespace std;
void countFreqBrute(const vector<int>& arr) {
    int n = arr.size();
    vector<bool> visited(n, false);
    for (int i = 0; i < n; i++) {
        if (visited[i]) continue;
        int count = 1;
        for (int j = i + 1; j < n; j++) {
            if (arr[i] == arr[j]) { visited[j] = true; count++; }
        }
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ visited array.
- **Why it's not good enough**: Quadratic nested loops.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Single pass Hash Map frequency counting.

### C++17 Code
```cpp
#include <vector>
#include <unordered_map>
using namespace std;

class Solution {
public:
    unordered_map<int, int> countFrequencies(const vector<int>& arr) {
        unordered_map<int, int> freqMap;
        for (int x : arr) {
            freqMap[x]++;
        }
        return freqMap;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ average time.
- **Space Complexity**: $\mathcal{O}(U)$ auxiliary space where $U \le N$ is the number of unique elements.
- **Why this is optimal**: Hash map provides $\mathcal{O}(1)$ average insertion and increment.

---

## 6. Dry Run

`arr = [10, 5, 10, 15, 10, 5]`

| Step | Action / State Change | Result |
|---|---|---|
| `Pass 1` | x = 10 -> freq[10] = 1 | Map: {10: 1} |
| `Pass 2` | x = 5  -> freq[5]  = 1 | Map: {10: 1, 5: 1} |
| `Pass 3` | x = 10 -> freq[10] = 2 | Map: {10: 2, 5: 1} |
| `Pass 4` | x = 15 -> freq[15] = 1 | Map: {10: 2, 5: 1, 15: 1} |
| `Pass 5` | x = 10 -> freq[10] = 3 | Map: {10: 3, 5: 1, 15: 1} |
| `Pass 6` | x = 5  -> freq[5]  = 2 | Final Freq: {10: 3, 5: 2, 15: 1} ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Empty array
- All elements identical
- Negative numbers in array.

### Common Bugs to Avoid
- Assuming values fit in a static array when elements can be up to $10^9$ (must use `unordered_map`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why use unordered_map over map?**  
  **A**: `unordered_map` uses hash table ($\mathcal{O}(1)$ average), while `map` uses Red-Black Tree ($\mathcal{O}(\log N)$).


---

## 9. Tags & Related Problems

- **Tags**: `Hashing`, `Frequency`, `Easy`
- **Related problems to practice next**:
- **Find Highest & Lowest Frequency**: Min/Max frequency search.
