# Count Number of Nice Subarrays (Step 10.1 — Medium Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Count Number of Nice Subarrays](https://takeuforward.org/data-structure/count-number-of-nice-subarrays/)
- **Difficulty**: Medium
- **Statement**: Given an array of integers `nums` and an integer $k$. A continuous subarray is called nice if there are $k$ odd numbers in it. Return the number of nice subarrays.

---

## 1. Problem, Restated

Convert array to binary parity ($nums[i] \% 2$), reducing problem to Binary Subarrays with Sum $k$.

- **Input**: Array / String with constraints and threshold values.
- **Output**: Maximum length / count of valid subarrays / minimal window.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Replace every odd number with 1 and even number with 0. The problem becomes identical to **Binary Subarrays With Sum $k$**! Total nice subarrays $= \text{atMostOdds}(k) - \text{atMostOdds}(k - 1)$ in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

- **Underlying Pattern**: `Parity Reduction + $	ext{AtMost}(K) - 	ext{AtMost}(K-1)$ Sliding Window`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Prefix sum hash map tracking odd parity counts in $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
// Prefix sum map
```

### Java Code
```java
// Java equivalent
// Prefix sum map
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$.
- **Space Complexity**: $\mathcal{O}(N)$ map memory.
- **Why it's not good enough**: Uses extra memory.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard dynamic sliding window below directly achieves optimal $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Sliding Window $\text{AtMost}(K) - \text{AtMost}(K - 1)$ in $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
private:
    int countAtMost(const vector<int>& nums, int k) {
        if (k < 0) return 0;
        
        int left = 0, count = 0, oddCount = 0;
        int n = nums.size();
        
        for (int right = 0; right < n; right++) {
            oddCount += (nums[right] % 2);
            
            while (oddCount > k) {
                oddCount -= (nums[left] % 2);
                left++;
            }
            
            count += (right - left + 1);
        }
        
        return count;
    }

public:
    int numberOfSubarrays(vector<int>& nums, int k) {
        return countAtMost(nums, k) - countAtMost(nums, k - 1);
    }
};
```

### Java Code
```java
class Solution {

    int countAtMost(int[] nums, int k) {
        if (k < 0) return 0;
        
        int left = 0, count = 0, oddCount = 0;
        int n = nums.length;
        
        for (int right = 0; right < n; right++) {
            oddCount += (nums[right] % 2);
            
            while (oddCount > k) {
                oddCount -= (nums[left] % 2);
                left++;
            }
            
            count += (right - left + 1);
        }
        
        return count;
    }

    int numberOfSubarrays(int[] nums, int k) {
        return countAtMost(nums, k) - countAtMost(nums, k - 1);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ two passes.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Direct mathematical equivalence to binary sum sliding window.

---

## 6. Dry Run

`nums = [1, 1, 2, 1, 1], k = 3`

| Step | Action / State Change | Result |
|---|---|---|
| `Binary array` | `[1, 1, 0, 1, 1]`, goal = 3 | Parity transformed |
| `atMost(3)` | subarrays with <= 3 odds | Total = 14 |
| `atMost(2)` | subarrays with <= 2 odds | Total = 12 |
| `Exact(3)` | $14 - 12 = 2$ | Result = 2 (`[1, 1, 2, 1]` and `[1, 2, 1, 1]`) ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- No odd numbers present (returns 0).
- $k > N$ (returns 0).

### Common Bugs to Avoid
- Modulo operator with negative numbers (use `(nums[i] % 2 != 0)` or `(nums[i] & 1)`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does (nums[i] & 1) correctly extract odd parity?**  
  **A**: Bitwise LSB check `(x & 1)` is 1 for odd numbers and 0 for even numbers, which works cleanly across all positive integers.


---

## 9. Tags & Related Problems

- **Tags**: `Sliding Window`, `Two Pointers`, `LeetCode-1248`, `Medium`
- **Related problems to practice next**:
- **Binary Subarrays With Sum**: Direct equivalence.
