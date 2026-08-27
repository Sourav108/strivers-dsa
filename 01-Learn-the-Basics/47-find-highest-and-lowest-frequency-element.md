# Find the Highest & Lowest Frequency Element (Step 1.6 — Learn Basic Hashing)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Find the Highest & Lowest Frequency Element](https://takeuforward.org/arrays/find-the-highest-lowest-frequency-element/)
- **Difficulty**: Easy
- **Statement**: Given an array `arr` of $n$ integers, find the element with the highest frequency and the element with the lowest frequency. If multiple elements have identical frequency, return the smallest value.

---

## 1. Problem, Restated

Find $\text{argmax}$ and $\text{argmin}$ of frequencies with lexicographical tie-breaking.

- **Input**: Parameters specified.
- **Output**: Value or side-effect meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) Build frequency map `freq[x]`. 2) Iterate through map entries maintaining `maxFreq`, `minFreq`, `maxEle`, `minEle` with tie-breaking rules.

- **Underlying Pattern**: `Frequency Table Min/Max Extrema Scan`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Nested loops counting and tracking min/max in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
// O(N^2) nested loops
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Quadratic runtime.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Two-pass Hash Map scan with tie-breaking.

### C++17 Code
```cpp
#include <vector>
#include <unordered_map>
#include <climits>
using namespace std;

class Solution {
public:
    pair<int, int> getHighestAndLowestFreq(const vector<int>& arr) {
        unordered_map<int, int> freq;
        for (int x : arr) freq[x]++;
        
        int maxFreq = 0, minFreq = INT_MAX;
        int maxEle = -1, minEle = -1;
        
        for (const auto& [num, count] : freq) {
            // Highest frequency check (tie break with smaller element)
            if (count > maxFreq || (count == maxFreq && num < maxEle)) {
                maxFreq = count;
                maxEle = num;
            }
            // Lowest frequency check (tie break with smaller element)
            if (count < minFreq || (count == minFreq && num < minEle)) {
                minFreq = count;
                minEle = num;
            }
        }
        
        return {maxEle, minEle};
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(U)$ space for frequency map.
- **Why this is optimal**: Linear time frequency table traversal.

---

## 6. Dry Run

`arr = [1, 2, 3, 1, 1, 4]`

| Step | Action / State Change | Result |
|---|---|---|
| `Frequencies` | 1: 3, 2: 1, 3: 1, 4: 1 | Highest: 1 (count 3) |
| `Lowest` | 2, 3, 4 all count 1 -> Tie-breaker picks smallest: 2 | Result: {1, 2} ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- All elements have frequency 1 (returns min element for both).
- Single element array.

### Common Bugs to Avoid
- Not initializing `minFreq` with `INT_MAX`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is structured binding const auto& [num, count] preferred?**  
  **A**: It avoids copying pair objects and gives clean named access in C++17.


---

## 9. Tags & Related Problems

- **Tags**: `Hashing`, `Frequency`, `Easy`
- **Related problems to practice next**:
- **Counting Frequencies**: Base frequency map.
