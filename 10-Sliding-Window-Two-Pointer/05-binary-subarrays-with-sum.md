# Binary Subarrays With Sum (Exact goal sum via atMost(K) - atMost(K-1)) (Step 10.1 — Medium Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Binary Subarrays With Sum (Exact goal sum via atMost(K) - atMost(K-1))](https://takeuforward.org/data-structure/binary-subarrays-with-sum/)
- **Difficulty**: Medium
- **Statement**: Given a binary array `nums` and an integer `goal`, return the number of non-empty subarrays with a sum equal to `goal`.

---

## 1. Problem, Restated

Compute $\text{countExact}(goal) = \text{countAtMost}(goal) - \text{countAtMost}(goal - 1)$ in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

- **Input**: Array / String with constraints and threshold values.
- **Output**: Maximum length / count of valid subarrays / minimal window.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Direct two-pointer sliding window cannot easily count exact sums because adding zeros does not change the sum. However, counting subarrays with **sum $\le K$** is monotonic and trivial with sliding window: each valid window $[left, right]$ contributes $(right - left + 1)$ subarrays! Thus, $\text{Exact}(goal) = \text{atMost}(goal) - \text{atMost}(goal - 1)$!

- **Underlying Pattern**: `Exact Count Reduction ($	ext{Exact}(K) = 	ext{AtMost}(K) - 	ext{AtMost}(K-1)$)`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Prefix sum hash map in $\mathcal{O}(N)$ time and $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <vector>
#include <unordered_map>
using namespace std;
int numSubarraysWithSumHash(vector<int>& nums, int goal) {
    unordered_map<int, int> mp;
    mp[0] = 1;
    int sum = 0, count = 0;
    for (int x : nums) {
        sum += x;
        if (mp.count(sum - goal)) count += mp[sum - goal];
        mp[sum]++;
    }
    return count;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ hash map space.
- **Why it's not good enough**: Uses $\mathcal{O}(N)$ memory.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard dynamic sliding window below directly achieves optimal $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Sliding Window $\text{AtMost}(K) - \text{AtMost}(K - 1)$ in strict $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
private:
    int countAtMost(const vector<int>& nums, int goal) {
        if (goal < 0) return 0;
        
        int left = 0, count = 0, currentSum = 0;
        int n = nums.size();
        
        for (int right = 0; right < n; right++) {
            currentSum += nums[right];
            
            while (currentSum > goal) {
                currentSum -= nums[left];
                left++;
            }
            
            // All subarrays ending at 'right' starting from [left..right] have sum <= goal
            count += (right - left + 1);
        }
        
        return count;
    }

public:
    int numSubarraysWithSum(vector<int>& nums, int goal) {
        return countAtMost(nums, goal) - countAtMost(nums, goal - 1);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ two passes.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Exact difference reduction eliminates hash map allocation completely.

---

## 6. Dry Run

`nums = [1, 0, 1, 0, 1], goal = 2`

| Step | Action / State Change | Result |
|---|---|---|
| `atMost(2)` | Counts all subarrays with sum <= 2 | Total = 12 |
| `atMost(1)` | Counts all subarrays with sum <= 1 | Total = 8 |
| `Exact(2)` | $12 - 8 = 4$ | Result = 4 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $goal = 0$ (`atMost(-1)` returns 0 safely).
- Array of all zeros.

### Common Bugs to Avoid
- Forgetting `if (goal < 0) return 0;` guard in `countAtMost`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does (right - left + 1) count all subarrays ending at right?**  
  **A**: Because if subarray $[left, right]$ has sum $\le goal$, every smaller subarray $[left+1, right], [left+2, right], \dots, [right, right]$ ending at `right` also has sum $\le goal$ (since elements are non-negative). There are exactly $(right - left + 1)$ such subarrays.


---

## 9. Tags & Related Problems

- **Tags**: `Sliding Window`, `Two Pointers`, `Prefix Reduction`, `LeetCode-930`, `Medium`
- **Related problems to practice next**:
- **Count Number of Nice Subarrays**: Isomorphic problem.
