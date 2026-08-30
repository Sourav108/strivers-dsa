# Subarrays with K Different Integers (Step 10.2 — Hard Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Subarrays with K Different Integers](https://takeuforward.org/data-structure/subarrays-with-k-different-integers/)
- **Difficulty**: Hard
- **Statement**: Given an integer array `nums` and an integer $k$, return the number of good subarrays of `nums` (a good array has exactly $k$ different integers).

---

## 1. Problem, Restated

Compute $\text{Exact}(k) = \text{AtMost}(k) - \text{AtMost}(k - 1)$ using sliding window frequency counts.

- **Input**: Array / String with constraints and threshold values.
- **Output**: Maximum length / count of valid subarrays / minimal window.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Direct sliding window cannot maintain exact $k$ distinct elements because shrinking the window might decrease distinct count below $k$. But counting subarrays with **at most $k$ distinct elements** is strictly monotonic: for any valid window $[left, right]$, adding $nums[right]$ contributes exactly $(right - left + 1)$ valid subarrays! Thus, $\text{Exact}(k) = \text{atMost}(k) - \text{atMost}(k - 1)$ in $\mathcal{O}(N)$ time!

- **Underlying Pattern**: `Exact K-Distinct Reduction ($	ext{Exact}(K) = 	ext{AtMost}(K) - 	ext{AtMost}(K-1)$)`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Nested loops counting distinct elements with a set in $\mathcal{O}(N^2)$ time.

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
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(k)$.
- **Why it's not good enough**: Quadratic time causes TLE.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard dynamic sliding window below directly achieves optimal $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Sliding Window $\text{AtMost}(K) - \text{AtMost}(K - 1)$ with Direct Array / Hash Map in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
#include <vector>
#include <unordered_map>
using namespace std;

class Solution {
private:
    int atMostKDistinct(const vector<int>& nums, int k) {
        if (k <= 0) return 0;
        
        unordered_map<int, int> mp;
        int left = 0, count = 0;
        int n = nums.size();
        
        for (int right = 0; right < n; right++) {
            mp[nums[right]]++;
            
            while ((int)mp.size() > k) {
                mp[nums[left]]--;
                if (mp[nums[left]] == 0) {
                    mp.erase(nums[left]);
                }
                left++;
            }
            
            count += (right - left + 1);
        }
        
        return count;
    }

public:
    int subarraysWithKDistinct(vector<int>& nums, int k) {
        return atMostKDistinct(nums, k) - atMostKDistinct(nums, k - 1);
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    int atMostKDistinct(int[] nums, int k) {
        if (k <= 0) return 0;
        
        Map<Integer, Integer> mp = new HashMap<>();
        int left = 0, count = 0;
        int n = nums.length;
        
        for (int right = 0; right < n; right++) {
            mp[nums[right]]++;
            
            while (mp.length > k) {
                mp[nums[left]]--;
                if (mp[nums[left]] == 0) {
                    mp.remove(nums[left]);
                }
                left++;
            }
            
            count += (right - left + 1);
        }
        
        return count;
    }

    int subarraysWithKDistinct(int[] nums, int k) {
        return atMostKDistinct(nums, k) - atMostKDistinct(nums, k - 1);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ two passes.
- **Space Complexity**: $\mathcal{O}(k)$ hash map space.
- **Why this is optimal**: Reduction converts non-monotonic exact count into two monotonic sliding window passes.

---

## 6. Dry Run

`nums = [1, 2, 1, 2, 3], k = 2`

| Step | Action / State Change | Result |
|---|---|---|
| `atMost(2)` | Counts all subarrays with <= 2 distinct integers | Total = 12 |
| `atMost(1)` | Counts all subarrays with <= 1 distinct integer | Total = 5 |
| `Exact(2)` | $12 - 5 = 7$ | Result = 7 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $k = 1$ (`atMost(0)` returns 0).
- $k > N$ (returns 0).

### Common Bugs to Avoid
- Not returning 0 when $k \le 0$ in helper.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is AtMost monotonic while Exact is not?**  
  **A**: Expanding a window monotonically INCREASES or maintains distinct elements. If $[left, right]$ has $\le k$ distinct elements, every sub-segment ending at `right` also has $\le k$ distinct elements. Exact $k$ does not have this prefix-subset property!


---

## 9. Tags & Related Problems

- **Tags**: `Sliding Window`, `Two Pointers`, `Hash Map`, `LeetCode-992`, `Hard`
- **Related problems to practice next**:
- **Binary Subarrays With Sum**: Exact reduction.
