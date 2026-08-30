# Fruit Into Baskets (At most 2 distinct fruits) (Step 10.1 — Medium Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Fruit Into Baskets (At most 2 distinct fruits)](https://takeuforward.org/data-structure/fruit-into-baskets/)
- **Difficulty**: Medium
- **Statement**: You are visiting a farm that has a single row of fruit trees represented by an integer array `fruits`. You have two baskets, and each basket can only hold a single type of fruit. Return the maximum number of fruits you can pick in a contiguous subarray.

---

## 1. Problem, Restated

Find the length of the longest subarray containing at most $2$ distinct integers.

- **Input**: Array / String with constraints and threshold values.
- **Output**: Maximum length / count of valid subarrays / minimal window.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Equivalent to **Longest Subarray with at most 2 distinct elements**. Maintain `freqMap`. Expand `right`: `freqMap[fruits[right]]++`. While `freqMap.size() > 2`: decrement `freqMap[fruits[left]]--`, and if count reaches 0, erase it `freqMap.erase(fruits[left])`, then `left++`. Track `maxLen = max(maxLen, right - left + 1)`.

- **Underlying Pattern**: `Dynamic Sliding Window with Frequency Map ($\le 2$ Distinct Keys)`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Check all subarrays with a set in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
// O(N^2) brute search
```

### Java Code
```java
// Java equivalent
// O(N^2) brute search
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Quadratic time.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard dynamic sliding window below directly achieves optimal $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Sliding Window with Hash Map in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <vector>
#include <unordered_map>
#include <algorithm>
using namespace std;

class Solution {
public:
    int totalFruit(vector<int>& fruits) {
        unordered_map<int, int> basket; // at most 3 elements at any time
        int left = 0, maxFruits = 0;
        int n = fruits.size();
        
        for (int right = 0; right < n; right++) {
            basket[fruits[right]]++;
            
            while (basket.size() > 2) {
                basket[fruits[left]]--;
                if (basket[fruits[left]] == 0) {
                    basket.erase(fruits[left]);
                }
                left++;
            }
            
            maxFruits = max(maxFruits, right - left + 1);
        }
        
        return maxFruits;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    int totalFruit(int[] fruits) {
        Map<Integer, Integer> basket = new HashMap<>(); // at most 3 elements at any time
        int left = 0, maxFruits = 0;
        int n = fruits.length;
        
        for (int right = 0; right < n; right++) {
            basket[fruits[right]]++;
            
            while (basket.length > 2) {
                basket[fruits[left]]--;
                if (basket[fruits[left]] == 0) {
                    basket.remove(fruits[left]);
                }
                left++;
            }
            
            maxFruits = Math.max(maxFruits, right - left + 1);
        }
        
        return maxFruits;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ linear time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space (hash map contains at most 3 keys).
- **Why this is optimal**: Sliding window maintains at most 2 distinct keys dynamically.

---

## 6. Dry Run

`fruits = [1, 2, 3, 2, 2]`

| Step | Action / State Change | Result |
|---|---|---|
| `r=0 (1), r=1 (2)` | basket: `{1:1, 2:1}`, size=2 | len = 2 |
| `r=2 (3)` | basket: `{1:1, 2:1, 3:1}`, size=3 > 2 -> left pops 1 -> `{2:1, 3:1}` | len = 2 |
| `r=3 (2), r=4 (2)` | basket: `{2:3, 3:1}`, size=2 -> window `[3, 2, 2, 2]` | len = 4 |
| `Result` | Max fruits picked | `4` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Array with only 1 or 2 fruit types ($N \implies$ return $N$).
- Alternating fruits `[1, 2, 1, 2, 1]` ($N$).

### Common Bugs to Avoid
- Forgetting `basket.erase()` when count reaches 0 (leaves map size as 3!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is space complexity O(1) despite using unordered_map?**  
  **A**: Because the while loop triggers as soon as `basket.size() > 2`, meaning the map contains at most 3 distinct keys at any instant. $\mathcal{O}(3) = \mathcal{O}(1)$.


---

## 9. Tags & Related Problems

- **Tags**: `Sliding Window`, `Two Pointers`, `Hash Map`, `LeetCode-904`, `Medium`
- **Related problems to practice next**:
- **Longest Substring with At Most K Distinct**: Generalized K.
