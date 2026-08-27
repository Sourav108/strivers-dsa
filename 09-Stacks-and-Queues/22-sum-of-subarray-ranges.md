# Sum of Subarray Ranges (Step 9.3 — Monotonic Stack / Queue)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Sum of Subarray Ranges](https://takeuforward.org/data-structure/sum-of-subarray-ranges/)
- **Difficulty**: Medium
- **Statement**: Given an integer array `nums`, return the sum of all subarray ranges (range of a subarray is $\max(b) - \min(b)$).

---

## 1. Problem, Restated

Compute $\sum (\max - \min) = \sum \text{Subarray Maximums} - \sum \text{Subarray Minimums}$ in $\mathcal{O}(N)$ time.

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Resulting vector of elements / integer answer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Range sum $= \sum (\max - \min) = \sum \max - \sum \min$. 1) Calculate $\sum \min$ using Previous/Next Less Element monotonic stack (Problem 20). 2) Calculate $\sum \max$ using Previous/Next Greater Element monotonic stack. 3) Return `sumMax - sumMin` in $\mathcal{O}(N)$ time.

- **Underlying Pattern**: `Contribution Decomposition (Sum of Maxes - Sum of Mins)`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Nested loops finding min and max for all $N^2$ subarrays in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
// O(N^2) brute loop
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Quadratic time.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — monotonic data structure below directly achieves optimal $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Dual Monotonic Stack Linear Contribution in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
#include <vector>
#include <stack>
using namespace std;

class Solution {
private:
    long long sumSubarrayMins(const vector<int>& arr) {
        int n = arr.size();
        vector<int> ple(n), nle(n);
        stack<int> st;
        
        for (int i = 0; i < n; i++) {
            while (!st.empty() && arr[st.top()] > arr[i]) st.pop();
            ple[i] = st.empty() ? (i + 1) : (i - st.top());
            st.push(i);
        }
        while (!st.empty()) st.pop();
        for (int i = n - 1; i >= 0; i--) {
            while (!st.empty() && arr[st.top()] >= arr[i]) st.pop();
            nle[i] = st.empty() ? (n - i) : (st.top() - i);
            st.push(i);
        }
        
        long long sum = 0;
        for (int i = 0; i < n; i++) {
            sum += 1LL * arr[i] * ple[i] * nle[i];
        }
        return sum;
    }
    
    long long sumSubarrayMaxs(const vector<int>& arr) {
        int n = arr.size();
        vector<int> pge(n), nge(n);
        stack<int> st;
        
        for (int i = 0; i < n; i++) {
            while (!st.empty() && arr[st.top()] < arr[i]) st.pop();
            pge[i] = st.empty() ? (i + 1) : (i - st.top());
            st.push(i);
        }
        while (!st.empty()) st.pop();
        for (int i = n - 1; i >= 0; i--) {
            while (!st.empty() && arr[st.top()] <= arr[i]) st.pop();
            nge[i] = st.empty() ? (n - i) : (st.top() - i);
            st.push(i);
        }
        
        long long sum = 0;
        for (int i = 0; i < n; i++) {
            sum += 1LL * arr[i] * pge[i] * nge[i];
        }
        return sum;
    }

public:
    long long subArrayRanges(vector<int>& nums) {
        return sumSubarrayMaxs(nums) - sumSubarrayMins(nums);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ linear time.
- **Space Complexity**: $\mathcal{O}(N)$ stack space.
- **Why this is optimal**: Decomposes 2D range problem into two independent 1D contribution passes.

---

## 6. Dry Run

`nums = [1, 2, 3]`

| Step | Action / State Change | Result |
|---|---|---|
| `sumMax` | $1(1) + 2(2) + 3(3) = 1 + 4 + 9 = 14$ | sumMax = 14 |
| `sumMin` | $1(3) + 2(2) + 3(1) = 3 + 4 + 3 = 10$ | sumMin = 10 |
| `Result` | $14 - 10 = 4$ | Result = 4 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single element array (sum = 0).
- 64-bit integer overflow.

### Common Bugs to Avoid
- Modulo operator: LeetCode 2104 does NOT require modulo (return `long long`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is linearity of summation applicable here?**  
  **A**: Because $\sum_{S} (\max(S) - \min(S)) = \sum_S \max(S) - \sum_S \min(S)$. Each term can be computed independently using monotonic stacks.


---

## 9. Tags & Related Problems

- **Tags**: `Stack`, `Monotonic Stack`, `Contribution`, `LeetCode-2104`, `Medium`
- **Related problems to practice next**:
- **Sum of Subarray Minimums**: Sub-routine.
