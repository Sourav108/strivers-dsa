# Next Smaller Element (Immediate smaller) (Step 9.3 — Monotonic Stack / Queue)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Next Smaller Element (Immediate smaller)](https://takeuforward.org/data-structure/next-smaller-element/)
- **Difficulty**: Easy
- **Statement**: Given an array `arr` of $N$ integers, find the Next Smaller Element (NSE) to the right for every element. If no smaller element exists, output -1.

---

## 1. Problem, Restated

Find the first element to the right strictly smaller than `arr[i]` using a monotonic increasing stack.

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Resulting vector of elements / integer answer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Traverse array from right to left. Maintain stack in strictly increasing order from top to bottom. While `!st.empty() && st.top() >= arr[i]`, pop elements. If stack is empty, NSE is -1; else NSE is `st.top()`. Push `arr[i]`.

- **Underlying Pattern**: `Monotonic Increasing Stack (Right-to-Left Traversal)`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Nested loops checking rightwards in $\mathcal{O}(N^2)$ time.

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
- **Time Complexity**: $\mathcal{O}(N^2)$.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Quadratic time.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — monotonic data structure below directly achieves optimal $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Monotonic Increasing Stack in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
#include <vector>
#include <stack>
using namespace std;

class Solution {
public:
    vector<int> prevSmaller(vector<int>& arr) {
        int n = arr.size();
        vector<int> nse(n, -1);
        stack<int> st; // monotonic increasing
        
        for (int i = n - 1; i >= 0; i--) {
            while (!st.empty() && st.top() >= arr[i]) {
                st.pop();
            }
            
            nse[i] = st.empty() ? -1 : st.top();
            st.push(arr[i]);
        }
        
        return nse;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    int[] prevSmaller(int[] arr) {
        int n = arr.length;
        int[] nse = new int[n];
        Stack<Integer> st = new Stack<>(); // monotonic increasing
        
        for (int i = n - 1; i >= 0; i--) {
            while (!st.isEmpty() && st.peek() >= arr[i]) {
                st.pop();
            }
            
            nse[i] = st.isEmpty() ? -1 : st.peek();
            st.push(arr[i]);
        }
        
        return nse;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(N)$ stack space.
- **Why this is optimal**: Every element pushed and popped at most once.

---

## 6. Dry Run

`arr = [4, 5, 2, 10, 8]`

| Step | Action / State Change | Result |
|---|---|---|
| `i = 4 (8)` | st empty -> nse[4] = -1, push 8 | st: `[8]` |
| `i = 3 (10)` | st.top() = 8 < 10 -> nse[3] = 8, push 10 | st: `[8, 10]` |
| `i = 2 (2)` | pop 10, pop 8 -> st empty -> nse[2] = -1, push 2 | st: `[2]` |
| `i = 1 (5)` | st.top() = 2 < 5 -> nse[1] = 2, push 5 | st: `[2, 5]` |
| `i = 0 (4)` | pop 5 -> st.top() = 2 -> nse[0] = 2 | Result: `[2, 2, -1, 8, -1]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Strictly increasing array (all NSEs -1 except none).
- Single element.

### Common Bugs to Avoid
- Using `>` instead of `>=` in `st.top() >= arr[i]`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How does NSE differ from NGE?**  
  **A**: NGE uses a monotonic decreasing stack (`pop <= arr[i]`) while NSE uses a monotonic increasing stack (`pop >= arr[i]`).


---

## 9. Tags & Related Problems

- **Tags**: `Stack`, `Monotonic Stack`, `Easy`
- **Related problems to practice next**:
- **Largest Rectangle in Histogram**: Uses NSE.
