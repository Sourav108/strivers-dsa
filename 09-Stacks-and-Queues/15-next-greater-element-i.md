# Next Greater Element I (Right side lookup) (Step 9.3 — Monotonic Stack / Queue)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Next Greater Element I (Right side lookup)](https://takeuforward.org/data-structure/next-greater-element-using-stack/)
- **Difficulty**: Medium
- **Statement**: Given two arrays `nums1` and `nums2` where `nums1` is a subset of `nums2`, find the next greater element for each element of `nums1` in `nums2`. If none exists, return -1.

---

## 1. Problem, Restated

Find the first element to the right strictly greater than `x` using a monotonic decreasing stack.

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Resulting vector of elements / integer answer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Iterate through `nums2` from right to left ($i = N-1$ down to 0). While stack is not empty and `st.top() <= nums2[i]`, pop elements (they can never be the NGE for any element to the left of `nums2[i]`). If stack is empty, NGE is -1; else NGE is `st.top()`. Map `ngeMap[nums2[i]] = nge`, push `nums2[i]`. Answer `nums1` queries via hash map in $\mathcal{O}(1)$.

- **Underlying Pattern**: `Monotonic Decreasing Stack (Right-to-Left Traversal)`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Nested loops for each element scanning rightwards in $\mathcal{O}(N_1 \times N_2)$ time.

### C++17 Code
```cpp
#include <vector>
using namespace std;
vector<int> nextGreaterBrute(vector<int>& nums1, vector<int>& nums2) {
    vector<int> ans;
    for (int x : nums1) {
        int idx = 0;
        while (nums2[idx] != x) idx++;
        int nge = -1;
        for (int j = idx + 1; j < (int)nums2.size(); j++) {
            if (nums2[j] > x) { nge = nums2[j]; break; }
        }
        ans.push_back(nge);
    }
    return ans;
}
```

### Java Code
```java
import java.util.*;

class Solution {
    int[] nextGreaterBrute(int[] nums1, int[] nums2) {
        List<Integer> ans = new ArrayList<>();
        for (int x : nums1) {
            int idx = 0;
            while (nums2[idx] != x) idx++;
            int nge = -1;
            for (int j = idx + 1; j < nums2.length; j++) {
                if (nums2[j] > x) { nge = nums2[j]; break; }
            }
            ans.add(nge);
        }
        return ans;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N_1 \times N_2)$ time.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Repeated rightward linear scans.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — monotonic data structure below directly achieves optimal $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Monotonic Decreasing Stack + Hash Map in $\mathcal{O}(N_1 + N_2)$ time.

### C++17 Code
```cpp
#include <vector>
#include <stack>
#include <unordered_map>
using namespace std;

class Solution {
public:
    vector<int> nextGreaterElement(vector<int>& nums1, vector<int>& nums2) {
        unordered_map<int, int> ngeMap;
        stack<int> st; // stores elements in monotonic decreasing order
        
        // Traverse nums2 from right to left
        for (int i = (int)nums2.size() - 1; i >= 0; i--) {
            while (!st.empty() && st.top() <= nums2[i]) {
                st.pop();
            }
            
            ngeMap[nums2[i]] = st.empty() ? -1 : st.top();
            st.push(nums2[i]);
        }
        
        vector<int> ans;
        ans.reserve(nums1.size());
        for (int x : nums1) {
            ans.push_back(ngeMap[x]);
        }
        
        return ans;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    int[] nextGreaterElement(int[] nums1, int[] nums2) {
        Map<Integer, Integer> ngeMap = new HashMap<>();
        Stack<Integer> st = new Stack<>(); // stores elements in monotonic decreasing order
        
        // Traverse nums2 from right to left
        for (int i = nums2.length - 1; i >= 0; i--) {
            while (!st.isEmpty() && st.peek() <= nums2[i]) {
                st.pop();
            }
            
            ngeMap[nums2[i]] = st.isEmpty() ? -1 : st.peek();
            st.push(nums2[i]);
        }
        
        List<Integer> ans = new ArrayList<>();
        ans.reserve(nums1.length);
        for (int x : nums1) {
            ans.add(ngeMap[x]);
        }
        
        return ans;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N_1 + N_2)$ time (each element is pushed and popped at most once).
- **Space Complexity**: $\mathcal{O}(N_2)$ stack and hash map space.
- **Why this is optimal**: Monotonic invariant discards useless smaller elements permanently.

---

## 6. Dry Run

`nums2 = [1, 3, 4, 2]`

| Step | Action / State Change | Result |
|---|---|---|
| `i = 3 (val 2)` | st empty -> NGE = -1, push 2 | st: `[2]` |
| `i = 2 (val 4)` | pop 2 (2 <= 4) -> st empty -> NGE = -1, push 4 | st: `[4]` |
| `i = 1 (val 3)` | st.top() = 4 > 3 -> NGE = 4, push 3 | st: `[4, 3]` |
| `i = 0 (val 1)` | st.top() = 3 > 1 -> NGE = 3, push 1 | st: `[4, 3, 1]` |
| `Result Map` | `{2:-1, 4:-1, 3:4, 1:3}` | Complete ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- No greater element (returns -1).
- Array strictly decreasing (all NGEs -1).

### Common Bugs to Avoid
- Using `<` instead of `<=` in `st.top() <= nums2[i]` (leaves duplicate equal elements in stack).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does each element enter and leave the stack at most once?**  
  **A**: Because an element is pushed exactly once during the loop, and once popped by a larger element, it is never pushed again. Thus total stack operations $\le 2N$, giving strict $\mathcal{O}(N)$ amortized time.


---

## 9. Tags & Related Problems

- **Tags**: `Stack`, `Monotonic Stack`, `LeetCode-496`, `Medium`
- **Related problems to practice next**:
- **Next Greater Element II**: Circular array.
