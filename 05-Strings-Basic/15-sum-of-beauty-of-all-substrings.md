# Sum of Beauty of All Substrings (Step 5.2 — Medium String Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Sum of Beauty of All Substrings](https://takeuforward.org/strings/sum-of-beauty-of-all-substrings/)
- **Difficulty**: Medium
- **Statement**: The beauty of a string is the difference in frequencies between the most frequent and least frequent characters (with non-zero frequency). Given a string $s$, return the sum of beauty of all of its substrings.

---

## 1. Problem, Restated

Compute $\sum (\max(\text{freq}) - \min(\text{freq}>0))$ across all $N(N+1)/2$ substrings.

- **Input**: Problem parameters.
- **Output**: Transformed string or calculated integer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Fix the starting index $i \in [0, n-1]$. Expand the ending index $j$ from $i$ to $n-1$. Maintain an updated frequency array `freq[26]`. For each step $j$, calculate `maxFreq` and `minFreq` by scanning the 26 entries (takes $\mathcal{O}(26) = \mathcal{O}(1)$ time). Add `maxFreq - minFreq` to total beauty. Total time: $\mathcal{O}(N^2 \times 26) = \mathcal{O}(N^2)$ in $\mathcal{O}(1)$ space.

- **Underlying Pattern**: `Subarray Frequency Expansion & 26-Array Extrema Calculation`.

---

## 3. Approach 1 — Brute Force / Naive

### Idea
Generate substrings and count frequencies from scratch in $\mathcal{O}(N^3)$ time.

### C++17 Code
```cpp
// O(N^3) recalculating frequencies on every substring
```

### Java Code
```java
// Java equivalent
// O(N^3) recalculating frequencies on every substring
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^3)$ time.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Redundant re-scanning of characters.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Incremental frequency update with 26-element array in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
#include <string>
#include <vector>
#include <algorithm>
#include <climits>
using namespace std;

class Solution {
public:
    int beautySum(string s) {
        int n = s.length();
        int totalBeauty = 0;
        
        for (int i = 0; i < n; i++) {
            int freq[26] = {0};
            
            for (int j = i; j < n; j++) {
                freq[s[j] - 'a']++;
                
                int maxF = 0;
                int minF = INT_MAX;
                
                // Scan 26 lowercase alphabet frequencies
                for (int k = 0; k < 26; k++) {
                    if (freq[k] > 0) {
                        maxF = max(maxF, freq[k]);
                        minF = min(minF, freq[k]);
                    }
                }
                
                totalBeauty += (maxF - minF);
            }
        }
        
        return totalBeauty;
    }
};
```

### Java Code
```java
class Solution {

    int beautySum(String s) {
        int n = s.length();
        int totalBeauty = 0;
        
        for (int i = 0; i < n; i++) {
            int freq[26] = {0};
            
            for (int j = i; j < n; j++) {
                freq[s[j] - 'a']++;
                
                int maxF = 0;
                int minF = Integer.MAX_VALUE;
                
                // Scan 26 lowercase alphabet frequencies
                for (int k = 0; k < 26; k++) {
                    if (freq[k] > 0) {
                        maxF = Math.max(maxF, freq[k]);
                        minF = Math.min(minF, freq[k]);
                    }
                }
                
                totalBeauty += (maxF - minF);
            }
        }
        
        return totalBeauty;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2 \times 26) = \mathcal{O}(N^2)$ time (runs in $\approx 40\text{ms}$ for $N = 500$).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space (26 integers).
- **Why this is optimal**: Incremental expansion reuses frequency counts from previous substring prefix.

---

## 6. Dry Run

$s = \text{"aabcb"}$

| Step | Action / State Change | Result |
|---|---|---|
| `Substrings with beauty > 0` | "aab" (2-1=1), "aabc" (2-1=1), "aabcb" (2-1=1), "abcb" (2-1=1), "bcb" (2-1=1) | Sum of Beauty = 5 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Strings with all identical characters `"aaaa"` (beauty is always 0).
- String length $\le 2$ (beauty is always 0).

### Common Bugs to Avoid
- Initializing `minF = 0` (minFreq would always stay 0; must only consider characters with `freq > 0`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is scanning 26 letters inside O(N^2) acceptable?**  
  **A**: Because the alphabet size $|\Sigma| = 26$ is a fixed constant. $500^2 \times 26 = 6.5 \times 10^6$ operations, which easily runs in $< 50\text{ms}$.


---

## 9. Tags & Related Problems

- **Tags**: `Strings`, `Hashing`, `LeetCode-1781`, `Medium`
- **Related problems to practice next**:
- **Find Highest & Lowest Frequency**: Extrema calculation.
