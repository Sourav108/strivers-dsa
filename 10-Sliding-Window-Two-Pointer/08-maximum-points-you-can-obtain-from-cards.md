# Maximum Points You Can Obtain from Cards (Step 10.1 — Medium Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Maximum Points You Can Obtain from Cards](https://takeuforward.org/data-structure/maximum-points-you-can-obtain-from-cards/)
- **Difficulty**: Medium
- **Statement**: There are several cards arranged in a row. In one step, you can take one card from the beginning or from the end of the row. You have to take exactly $k$ cards. Return the maximum score you can obtain.

---

## 1. Problem, Restated

Taking $k$ cards from ends is equivalent to finding a contiguous subarray of size $(N - k)$ with MINIMUM sum.

- **Input**: Array / String with constraints and threshold values.
- **Output**: Maximum length / count of valid subarrays / minimal window.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Taking $k$ cards from the two ends leaves an intact contiguous middle subarray of size $W = N - k$. To MAXIMIZE the sum of $k$ cards, we must MINIMIZE the sum of the remaining $(N - k)$ cards! Compute total sum of all cards. Slide a fixed window of size $N - k$, find `minSubarraySum`, and return $\text{totalSum} - \text{minSubarraySum}$ in $\mathcal{O}(N)$ time.

- **Underlying Pattern**: `Complementary Inverted Fixed Sliding Window of Size $(N - k)$`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Recursion trying pick from left vs pick from right in $\mathcal{O}(2^k)$ time.

### C++17 Code
```cpp
// O(2^k) recursion TLE
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^k)$ exponential.
- **Space Complexity**: $\mathcal{O}(k)$.
- **Why it's not good enough**: Exponential branching.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard dynamic sliding window below directly achieves optimal $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Fixed Sliding Window of size $(N - k)$ in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
#include <vector>
#include <numeric>
#include <algorithm>
using namespace std;

class Solution {
public:
    int maxScore(vector<int>& cardPoints, int k) {
        int n = cardPoints.size();
        int totalSum = accumulate(cardPoints.begin(), cardPoints.end(), 0);
        
        if (k == n) return totalSum;
        
        int windowSize = n - k;
        int currentWindowSum = 0;
        
        // Compute sum of first window of size (n - k)
        for (int i = 0; i < windowSize; i++) {
            currentWindowSum += cardPoints[i];
        }
        
        int minWindowSum = currentWindowSum;
        
        // Slide window across remaining array
        for (int i = windowSize; i < n; i++) {
            currentWindowSum += cardPoints[i] - cardPoints[i - windowSize];
            minWindowSum = min(minWindowSum, currentWindowSum);
        }
        
        return totalSum - minWindowSum;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Fixed window subtraction eliminates 2-ended branching.

---

## 6. Dry Run

`cardPoints = [1, 2, 3, 4, 5, 6, 1], k = 3` ($N = 7$, window size $W = 4$)

| Step | Action / State Change | Result |
|---|---|---|
| `Total Sum` | 1+2+3+4+5+6+1 = 22 | total = 22 |
| `Window 0 `[1, 2, 3, 4]`` | sum = 10 | minSum = 10 |
| `Window 1 `[2, 3, 4, 5]`` | sum = 14 | minSum = 10 |
| `Window 2 `[3, 4, 5, 6]`` | sum = 18 | minSum = 10 |
| `Window 3 `[4, 5, 6, 1]`` | sum = 16 | minSum = 10 |
| `Result` | $22 - 10 = 12$ (cards picked: 1, 6, 5) | Max Score = 12 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $k == N$ (returns total array sum).
- $k = 1$ (returns $\max(nums[0], nums[N-1])$).

### Common Bugs to Avoid
- Forgetting `k == n` edge case (window size becomes 0).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Can this be implemented by taking first k elements and swapping left-for-right?**  
  **A**: Yes! Start with sum of first $k$ elements. In $k$ steps, subtract `cardPoints[k - 1 - i]` and add `cardPoints[n - 1 - i]`, tracking the maximum sum.


---

## 9. Tags & Related Problems

- **Tags**: `Sliding Window`, `Two Pointers`, `Prefix Sum`, `LeetCode-1423`, `Medium`
- **Related problems to practice next**:
- **Sliding Window Maximum**: Fixed window.
