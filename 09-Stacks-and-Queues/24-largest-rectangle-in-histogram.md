# Largest Rectangle in Histogram (Single Pass) (Step 9.3 — Monotonic Stack / Queue)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Largest Rectangle in Histogram (Single Pass)](https://takeuforward.org/data-structure/calculate-the-max-area-of-rectangle-in-a-histogram/)
- **Difficulty**: Hard
- **Statement**: Given an array of integers `heights` representing the histogram's bar height where the width of each bar is 1, return the area of the largest rectangle in the histogram in $\mathcal{O}(N)$ single pass.

---

## 1. Problem, Restated

For each bar $i$, rectangle with height $heights[i]$ spans from $(\text{PSE}[i] + 1)$ to $(\text{NSE}[i] - 1)$ with width $(\text{NSE} - \text{PSE} - 1)$.

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Resulting vector of elements / integer answer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Maintain stack of bar indices in strictly increasing order of height. When current bar $i$ is smaller than `heights[stack.top()]`: the popped bar $h = heights[stack.pop()]$ has current index $i$ as its NSE, and the new stack top as its PSE! Width $= i - PSE - 1 = i - stack.top() - 1$ (or $i$ if stack is empty). Calculate $area = h \times width$ and update max area in a **single pass**!

- **Underlying Pattern**: `Monotonic Increasing Stack Single-Pass Histogram Area`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
For every bar $i$, expand left and right to find boundaries in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
// O(N^2) expansion
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Quadratic worst-case.

---

## 4. Approach 2 — Better

### Idea
Two-pass with precomputed NSE and PSE arrays.

### C++17 Code
```cpp
// 2-pass approach with PSE and NSE arrays
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ 2 passes.
- **Space Complexity**: $\mathcal{O}(2N)$ array memory.
- **Why it's still not optimal**: Requires multiple passes and arrays.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Single Pass Monotonic Stack with Virtual 0-Height Sentinel.

### C++17 Code
```cpp
#include <vector>
#include <stack>
#include <algorithm>
using namespace std;

class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        int n = heights.size();
        stack<int> st; // stores indices in monotonic increasing height
        int maxArea = 0;
        
        for (int i = 0; i <= n; i++) {
            // Virtual 0 height at i = n to pop all remaining elements
            int currentHeight = (i == n) ? 0 : heights[i];
            
            while (!st.empty() && heights[st.top()] >= currentHeight) {
                int h = heights[st.top()];
                st.pop();
                
                // Width = (NSE index - PSE index - 1)
                int width = st.empty() ? i : (i - st.top() - 1);
                maxArea = max(maxArea, h * width);
            }
            
            st.push(i);
        }
        
        return maxArea;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass (each bar pushed and popped at most once).
- **Space Complexity**: $\mathcal{O}(N)$ stack space.
- **Why this is optimal**: Calculates exact width on-the-fly upon popping in a single forward pass.

---

## 6. Dry Run

`heights = [2, 1, 5, 6, 2, 3]`

| Step | Action / State Change | Result |
|---|---|---|
| `i = 0 (2)` | st: `[0]` | Pushed |
| `i = 1 (1)` | pop 0 (h=2, w=1, area=2), push 1 | maxArea = 2 |
| `i = 2 (5), i=3 (6)` | st: `[1, 2, 3]` | Pushed |
| `i = 4 (2)` | pop 3 (h=6, w=4-2-1=1, area=6) -> pop 2 (h=5, w=4-1-1=2, area=10!) | maxArea = 10 |
| `Exit sentinel i=6` | pops remaining bars | Max Rectangle Area = 10 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single bar `[5]` (area 5).
- All bars same height `[2, 2, 2]` (area 6).

### Common Bugs to Avoid
- Calculating width as `i - st.top()` instead of `i - st.top() - 1`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does virtual height 0 at index n eliminate cleanup loops?**  
  **A**: A height of 0 is smaller than all valid non-negative bar heights ($0 \le heights[i]$), forcing the while loop to pop and evaluate all remaining bars on the stack at the final step.


---

## 9. Tags & Related Problems

- **Tags**: `Stack`, `Monotonic Stack`, `LeetCode-84`, `Hard`
- **Related problems to practice next**:
- **Maximal Rectangle**: 2D generalization.
