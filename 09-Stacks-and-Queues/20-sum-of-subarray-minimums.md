# Sum of Subarray Minimums (Monotonic Stack contribution) (Step 9.3 — Monotonic Stack / Queue)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Sum of Subarray Minimums (Monotonic Stack contribution)](https://takeuforward.org/data-structure/sum-of-subarray-minimums/)
- **Difficulty**: Medium
- **Statement**: Given an array of integers `arr`, find the sum of `min(b)`, where `b` ranges over every (contiguous) subarray of `arr`. Since the answer may be large, return the answer modulo $10^9 + 7$.

---

## 1. Problem, Restated

Compute the contribution of each element $arr[i]$ as the minimum: $arr[i] \times (i - \text{PLE}[i]) \times (\text{NLE}[i] - i)$.

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Resulting vector of elements / integer answer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Instead of generating $N^2$ subarrays, calculate how many subarrays have $arr[i]$ as their minimum! Let $\text{left}[i]$ be the distance to the Previous Less Element (PLE), and $\text{right}[i]$ be distance to Next Less Element (NLE). Total subarrays where $arr[i]$ is minimum $= \text{left}[i] \times \text{right}[i]$. Total contribution $= arr[i] \times \text{left}[i] \times \text{right}[i]$. Use strictly less on left and less-or-equal on right to avoid double-counting duplicate minimums.

- **Underlying Pattern**: `Contribution Technique + Monotonic Stack (Previous/Next Less Element)`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Nested loops finding minimum of every subarray in $\mathcal{O}(N^2)$ time.

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
- **Why it's not good enough**: Quadratic time causes TLE for $N = 3 \times 10^4$.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — monotonic data structure below directly achieves optimal $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
PLE and NLE Monotonic Stack Contribution in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
#include <vector>
#include <stack>
using namespace std;

class Solution {
public:
    int sumSubarrayMins(vector<int>& arr) {
        int n = arr.size();
        const int MOD = 1e9 + 7;
        
        vector<int> ple(n), nle(n);
        stack<int> st;
        
        // Find Previous Less Element (PLE) - strictly less
        for (int i = 0; i < n; i++) {
            while (!st.empty() && arr[st.top()] > arr[i]) {
                st.pop();
            }
            ple[i] = st.empty() ? (i + 1) : (i - st.top());
            st.push(i);
        }
        
        while (!st.empty()) st.pop();
        
        // Find Next Less Element (NLE) - less or equal (handles duplicates)
        for (int i = n - 1; i >= 0; i--) {
            while (!st.empty() && arr[st.top()] >= arr[i]) {
                st.pop();
            }
            nle[i] = st.empty() ? (n - i) : (st.top() - i);
            st.push(i);
        }
        
        // Compute total sum of contributions
        long long totalSum = 0;
        for (int i = 0; i < n; i++) {
            long long count = (1LL * ple[i] * nle[i]) % MOD;
            long long contribution = (count * arr[i]) % MOD;
            totalSum = (totalSum + contribution) % MOD;
        }
        
        return totalSum;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    int sumSubarrayMins(int[] arr) {
        int n = arr.length;
        int MOD = 1e9 + 7;
        
        int[] ple(n), nle(n);
        Stack<Integer> st = new Stack<>();
        
        // Find Previous Less Element (PLE) - strictly less
        for (int i = 0; i < n; i++) {
            while (!st.isEmpty() && arr[st.peek()] > arr[i]) {
                st.pop();
            }
            ple[i] = st.isEmpty() ? (i + 1) : (i - st.peek());
            st.push(i);
        }
        
        while (!st.isEmpty()) st.pop();
        
        // Find Next Less Element (NLE) - less or equal (handles duplicates)
        for (int i = n - 1; i >= 0; i--) {
            while (!st.isEmpty() && arr[st.peek()] >= arr[i]) {
                st.pop();
            }
            nle[i] = st.isEmpty() ? (n - i) : (st.peek() - i);
            st.push(i);
        }
        
        // Compute total sum of contributions
        long totalSum = 0;
        for (int i = 0; i < n; i++) {
            long count = (1LL * ple[i] * nle[i]) % MOD;
            long contribution = (count * arr[i]) % MOD;
            totalSum = (totalSum + contribution) % MOD;
        }
        
        return totalSum;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ two passes.
- **Space Complexity**: $\mathcal{O}(N)$ stack and distance vectors.
- **Why this is optimal**: Calculates every element's global contribution in $\mathcal{O}(1)$ without subarray iterations.

---

## 6. Dry Run

`arr = [3, 1, 2, 4]`

| Step | Action / State Change | Result |
|---|---|---|
| `i = 0 (3)` | ple=1, nle=1 -> count = $1 \times 1 = 1$, contrib = $3 \times 1 = 3$ | contrib = 3 |
| `i = 1 (1)` | ple=2, nle=3 -> count = $2 \times 3 = 6$, contrib = $1 \times 6 = 6$ | contrib = 6 |
| `i = 2 (2)` | ple=1, nle=2 -> count = $1 \times 2 = 2$, contrib = $2 \times 2 = 4$ | contrib = 4 |
| `i = 3 (4)` | ple=1, nle=1 -> count = $1 \times 1 = 1$, contrib = $4 \times 1 = 4$ | contrib = 4 |
| `Total Sum` | $3 + 6 + 4 + 4 = 17$ | Result = 17 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Array with duplicates `[1, 1]` (strict `<` on one side and `<=` on other side avoids double-counting).

### Common Bugs to Avoid
- Using `<=` on BOTH PLE and NLE passes (double counts subarrays having duplicate minimums).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why must one side use strict '>' and the other side use '>='?**  
  **A**: To establish a unique representative minimum for subarrays with duplicate equal values. Strict on left and non-strict on right guarantees every subarray has exactly ONE identified minimum index!


---

## 9. Tags & Related Problems

- **Tags**: `Stack`, `Monotonic Stack`, `Contribution`, `LeetCode-907`, `Medium`
- **Related problems to practice next**:
- **Sum of Subarray Ranges**: Sum of max - sum of min.
