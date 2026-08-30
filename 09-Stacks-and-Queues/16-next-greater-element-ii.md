# Next Greater Element II (Circular Array) (Step 9.3 — Monotonic Stack / Queue)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Next Greater Element II (Circular Array)](https://takeuforward.org/data-structure/next-greater-element-ii/)
- **Difficulty**: Medium
- **Statement**: Given a circular integer array `nums`, return the next greater number for every element in `nums`. The search wraps around circularly to the beginning of the array.

---

## 1. Problem, Restated

Simulate circular array by iterating over hypothetical $2N$ concatenated array from index $2N-1$ down to 0.

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Resulting vector of elements / integer answer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

A circular array of size $N$ is equivalent to repeating the array twice $[0..N-1, 0..N-1]$ of size $2N$. Loop $i$ from $2N-1$ down to 0, using index `i % N`. In the first pass ($2N-1$ to $N$), populate the stack with future elements. In the second pass ($N-1$ to $0$), record the NGE for each position `i < N` in $\mathcal{O}(N)$ time!

- **Underlying Pattern**: `Virtual Double Array + Monotonic Decreasing Stack (`i % N`)`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Loop circularly $N$ times for each element in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
// O(N^2) circular loop
```

### Java Code
```java
// Java equivalent
// O(N^2) circular loop
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Quadratic worst-case.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — monotonic data structure below directly achieves optimal $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Virtual $2N$ Circular Traversal with Monotonic Stack in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
#include <vector>
#include <stack>
using namespace std;

class Solution {
public:
    vector<int> nextGreaterElements(vector<int>& nums) {
        int n = nums.size();
        vector<int> nge(n, -1);
        stack<int> st;
        
        // Traverse virtual 2*n array backwards
        for (int i = 2 * n - 1; i >= 0; i--) {
            int idx = i % n;
            
            while (!st.empty() && st.top() <= nums[idx]) {
                st.pop();
            }
            
            if (i < n) {
                nge[idx] = st.empty() ? -1 : st.top();
            }
            
            st.push(nums[idx]);
        }
        
        return nge;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    int[] nextGreaterElements(int[] nums) {
        int n = nums.length;
        int[] nge = new int[n];
        Stack<Integer> st = new Stack<>();
        
        // Traverse virtual 2n array backwards
        for (int i = 2 * n - 1; i >= 0; i--) {
            int idx = i % n;
            
            while (!st.isEmpty() && st.peek() <= nums[idx]) {
                st.pop();
            }
            
            if (i < n) {
                nge[idx] = st.isEmpty() ? -1 : st.peek();
            }
            
            st.push(nums[idx]);
        }
        
        return nge;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2N) = \mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ stack space.
- **Why this is optimal**: Virtual loop simulates array duplication without allocating $2N$ memory.

---

## 6. Dry Run

`nums = [1, 2, 1]` ($N = 3$, indices 5 down to 0)

| Step | Action / State Change | Result |
|---|---|---|
| `Pass 1 (i=5,4,3)` | Fills stack with circular elements | st primed |
| `i = 2 (val 1)` | st.top() = 2 > 1 -> nge[2] = 2 | nge[2] = 2 |
| `i = 1 (val 2)` | pop elements <= 2 -> st empty -> nge[1] = -1 | nge[1] = -1 |
| `i = 0 (val 1)` | st.top() = 2 > 1 -> nge[0] = 2 | nge[0] = 2 |
| `Result` | `[2, -1, 2]` | Circular NGE complete ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- All elements identical `[1, 1, 1]` (returns `[-1, -1, -1]`).
- Strictly increasing circular array.

### Common Bugs to Avoid
- Recording `nge[idx]` during pass 1 when $i \ge N$ (overwrites valid second-pass results).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is allocating nums concatenated with nums unnecessary?**  
  **A**: Modulo arithmetic `i % n` on loop range $[2n-1, 0]$ accesses elements virtually in $\mathcal{O}(1)$ space without creating a physical $2N$ array.


---

## 9. Tags & Related Problems

- **Tags**: `Stack`, `Monotonic Stack`, `Circular Array`, `LeetCode-503`, `Medium`
- **Related problems to practice next**:
- **Next Greater Element I**: Linear NGE.
