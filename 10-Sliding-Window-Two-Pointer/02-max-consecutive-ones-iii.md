# Max Consecutive Ones III (At most K zeroes flipped) (Step 10.1 — Medium Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Max Consecutive Ones III (At most K zeroes flipped)](https://takeuforward.org/data-structure/max-consecutive-ones-iii/)
- **Difficulty**: Medium
- **Statement**: Given a binary array `nums` and an integer $k$, return the maximum number of consecutive `1`s in the array if you can flip at most $k$ `0`s to `1`s.

---

## 1. Problem, Restated

Find the longest subarray containing at most $k$ zeros in $\mathcal{O}(N)$ time.

- **Input**: Array / String with constraints and threshold values.
- **Output**: Maximum length / count of valid subarrays / minimal window.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Maintain window $[left, right]$ tracking `zeroCount`. Expand `right`: if `nums[right] == 0`, `zeroCount++`. If `zeroCount > k`: if `nums[left] == 0`, `zeroCount--`, and advance `left++`. Since we only care about the MAXIMUM window length, the window never needs to shrink below its current max size! Final answer is $N - left$ (or $(right - left + 1)$ in standard sliding window).

- **Underlying Pattern**: `Dynamic Sliding Window with Non-Shrinking Window Optimization`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Nested loops counting zeros for all $N^2$ subarrays in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
// O(N^2) brute force
```

### Java Code
```java
// Java equivalent
// O(N^2) brute force
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Quadratic scans.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard dynamic sliding window below directly achieves optimal $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Sliding Window Zero Counter in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int longestOnes(vector<int>& nums, int k) {
        int left = 0, zeroCount = 0, maxLen = 0;
        int n = nums.size();
        
        for (int right = 0; right < n; right++) {
            if (nums[right] == 0) {
                zeroCount++;
            }
            
            // Shrink window if zeros exceed budget k
            while (zeroCount > k) {
                if (nums[left] == 0) {
                    zeroCount--;
                }
                left++;
            }
            
            maxLen = max(maxLen, right - left + 1);
        }
        
        return maxLen;
    }
};
```

### Java Code
```java
class Solution {

    int longestOnes(int[] nums, int k) {
        int left = 0, zeroCount = 0, maxLen = 0;
        int n = nums.length;
        
        for (int right = 0; right < n; right++) {
            if (nums[right] == 0) {
                zeroCount++;
            }
            
            // Shrink window if zeros exceed budget k
            while (zeroCount > k) {
                if (nums[left] == 0) {
                    zeroCount--;
                }
                left++;
            }
            
            maxLen = Math.max(maxLen, right - left + 1);
        }
        
        return maxLen;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time (each element visited at most twice).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Expands right and conditionally advances left, running in amortized $\mathcal{O}(1)$ per step.

---

## 6. Dry Run

`nums = [1, 1, 1, 0, 0, 0, 1, 1, 1, 1, 0], k = 2`

| Step | Action / State Change | Result |
|---|---|---|
| `r=0..4` | zeros = 2 <= 2 -> window `[1, 1, 1, 0, 0]` | len = 5 |
| `r=5 (0)` | zeros = 3 > 2 -> left advances to 4 (pop 0) -> zeros=2 | len = 5 |
| `r=6..9 (1s)` | window `[0, 1, 1, 1, 1]` -> len = 6 | maxLen = 6 |
| `Result` | Maximum consecutive 1s after 2 flips | `6` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $k = 0$ (standard Max Consecutive Ones).
- $k \ge N$ (returns $N$).

### Common Bugs to Avoid
- Incrementing `maxLen` before shrinking window when `zeroCount > k`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Can we optimize this to a non-shrinking window in O(N) single pass without while loop?**  
  **A**: Yes! Replace `while (zeroCount > k)` with `if (zeroCount > k) { if (nums[left] == 0) zeroCount--; left++; }`. The window size never decreases, and the final answer is simply $N - left$!


---

## 9. Tags & Related Problems

- **Tags**: `Sliding Window`, `Two Pointers`, `LeetCode-1004`, `Medium`
- **Related problems to practice next**:
- **Fruit Into Baskets**: At most K distinct elements.
