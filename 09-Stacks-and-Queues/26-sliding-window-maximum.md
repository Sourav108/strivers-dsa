# Sliding Window Maximum (Monotonic Deque O(N)) (Step 9.4 — Implementation Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Sliding Window Maximum (Monotonic Deque O(N))](https://takeuforward.org/data-structure/sliding-window-maximum/)
- **Difficulty**: Hard
- **Statement**: You are given an array of integers `nums`, and a sliding window of size $k$ moving from left to right. Return the max sliding window values in strict $\mathcal{O}(N)$ time.

---

## 1. Problem, Restated

Find the maximum in each window of size $k$ using a monotonic decreasing double-ended queue (`std::deque`).

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Resulting vector of elements / integer answer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Maintain a deque `dq` storing **indices** in monotonic decreasing order of values. For each index $i$: 1) Remove indices out of window: `if (!dq.empty() && dq.front() <= i - k) dq.pop_front()`. 2) Maintain monotonicity: `while (!dq.empty() && nums[dq.back()] <= nums[i]) dq.pop_back()`. 3) `dq.push_back(i)`. 4) When $i \ge k - 1$, window max is `nums[dq.front()]`!

- **Underlying Pattern**: `Monotonic Decreasing Deque Index Window Maintenance`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Scan window of size $k$ in $\mathcal{O}(N \times k)$ time.

### C++17 Code
```cpp
// O(N*k) brute loop
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times k)$ time.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: TLE for $N, k = 10^5$.

---

## 4. Approach 2 — Better

### Idea
Max-Heap / Multiset in O(N log k) time.

### C++17 Code
```cpp
// multiset approach O(N log k)
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log k)$.
- **Space Complexity**: $\mathcal{O}(k)$.
- **Why it's still not optimal**: Tree balancing overhead.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Monotonic Deque in strict $\mathcal{O}(N)$ time and $\mathcal{O}(k)$ space.

### C++17 Code
```cpp
#include <vector>
#include <deque>
using namespace std;

class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        int n = nums.size();
        deque<int> dq; // stores indices in monotonic decreasing order of nums[i]
        vector<int> result;
        result.reserve(n - k + 1);
        
        for (int i = 0; i < n; i++) {
            // 1. Remove indices that are out of the current window [i - k + 1, i]
            if (!dq.empty() && dq.front() <= i - k) {
                dq.pop_front();
            }
            
            // 2. Remove elements smaller than current element from back
            while (!dq.empty() && nums[dq.back()] <= nums[i]) {
                dq.pop_back();
            }
            
            // 3. Add current index
            dq.push_back(i);
            
            // 4. If window size has reached k, record maximum
            if (i >= k - 1) {
                result.push_back(nums[dq.front()]);
            }
        }
        
        return result;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ linear time (each index pushed and popped at most once).
- **Space Complexity**: $\mathcal{O}(k)$ deque space.
- **Why this is optimal**: Deque front always holds the exact maximum element in the active window.

---

## 6. Dry Run

`nums = [1, 3, -1, -3, 5, 3, 6, 7], k = 3`

| Step | Action / State Change | Result |
|---|---|---|
| `i=0 (1), i=1 (3)` | pop 0 -> dq: `[1(val 3)]` | dq: [1] |
| `i=2 (-1)` | dq: `[1, 2]`, window 0 ready -> max = 3 | ans: `[3]` |
| `i=3 (-3)` | dq: `[1, 2, 3]` -> max = 3 | ans: `[3, 3]` |
| `i=4 (5)` | pop expired idx 1, pop 3, 2 -> dq: `[4(val 5)]` -> max = 5 | ans: `[3, 3, 5]` |
| `Final Result` | `[3, 3, 5, 5, 6, 7]` | Sliding window max complete ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $k = 1$ (returns array unchanged).
- $k = N$ (returns single global max).

### Common Bugs to Avoid
- Storing values in deque instead of indices (cannot check expired window bounds `dq.front() <= i - k`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why must the deque store indices rather than values?**  
  **A**: Storing indices allows determining whether the maximum element has fallen out of the sliding window (`dq.front() <= i - k`) in $\mathcal{O}(1)$ time.


---

## 9. Tags & Related Problems

- **Tags**: `Deque`, `Sliding Window`, `Monotonic Deque`, `LeetCode-239`, `Hard`
- **Related problems to practice next**:
- **Sliding Window Maximum**: Deque pattern.
