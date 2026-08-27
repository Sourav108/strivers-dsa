# Trapping Rain Water (Two Pointers & Monotonic Stack) (Step 9.3 — Monotonic Stack / Queue)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Trapping Rain Water (Two Pointers & Monotonic Stack)](https://takeuforward.org/data-structure/trapping-rainwater/)
- **Difficulty**: Hard
- **Statement**: Given $n$ non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it can trap after raining.

---

## 1. Problem, Restated

Water trapped at index $i$ is $\min(\text{leftMax}[i], \text{rightMax}[i]) - \text{height}[i]$. Solve in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space using Two Pointers.

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Resulting vector of elements / integer answer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Water trapped depends strictly on the LOWER of the two boundaries $\min(\text{leftMax}, \text{rightMax})$. Maintain two pointers `left = 0` and `right = N-1`. If `height[left] <= height[right]`, the left boundary is guaranteed to be the limiting bottleneck! If `height[left] >= leftMax`, update `leftMax`; else trap `leftMax - height[left]`. Advance `left++`. Symmetric for `right--`.

- **Underlying Pattern**: `Two Pointer Invariant Bottleneck Bounding (`leftMax` vs `rightMax`)`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
For every bar $i$, find `leftMax` and `rightMax` via two linear loops in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
// O(N^2) brute force
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Quadratic time.

---

## 4. Approach 2 — Better

### Idea
Prefix Max and Suffix Max arrays in O(N) time and O(N) space.

### C++17 Code
```cpp
#include <vector>
using namespace std;
int trapPrefixSuffix(vector<int>& height) {
    int n = height.size();
    vector<int> leftMax(n), rightMax(n);
    leftMax[0] = height[0];
    for (int i = 1; i < n; i++) leftMax[i] = max(leftMax[i-1], height[i]);
    rightMax[n-1] = height[n-1];
    for (int i = n - 2; i >= 0; i--) rightMax[i] = max(rightMax[i+1], height[i]);
    int water = 0;
    for (int i = 0; i < n; i++) water += min(leftMax[i], rightMax[i]) - height[i];
    return water;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(2N)$ array space.
- **Why it's still not optimal**: Requires two auxiliary arrays.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Two Pointers Bottleneck Elimination in strict $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int trap(vector<int>& height) {
        int n = height.size();
        if (n <= 2) return 0;
        
        int left = 0, right = n - 1;
        int leftMax = 0, rightMax = 0;
        int totalWater = 0;
        
        while (left <= right) {
            if (height[left] <= height[right]) {
                if (height[left] >= leftMax) {
                    leftMax = height[left];
                } else {
                    totalWater += leftMax - height[left];
                }
                left++;
            } else {
                if (height[right] >= rightMax) {
                    rightMax = height[right];
                } else {
                    totalWater += rightMax - height[right];
                }
                right--;
            }
        }
        
        return totalWater;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Bottleneck invariant allows computing trapped water without precomputing prefix/suffix arrays.

---

## 6. Dry Run

`height = [0, 1, 0, 2, 1, 0, 1, 3, 2, 1, 2, 1]`

| Step | Action / State Change | Result |
|---|---|---|
| `left=0, right=11` | h[0]=0 <= h[11]=1 -> leftMax = 0, left=1 | water = 0 |
| `left=1, right=11` | h[1]=1 <= h[11]=1 -> leftMax = 1, left=2 | water = 0 |
| `left=2, right=11` | h[2]=0 <= h[11]=1 -> water += 1 - 0 = 1, left=3 | water = 1 |
| `Continues...` | Traps water at all valleys | Total water = 6 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N \le 2$ (cannot trap water $\implies 0$).
- Monotonically increasing or decreasing array (0 water).

### Common Bugs to Avoid
- Using `<` instead of `<=` in while loop (misses the peak boundary).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is height[left] <= height[right] sufficient to guarantee leftMax is the bottleneck?**  
  **A**: Because if $height[left] \le height[right]$, there is guaranteed to be at least one boundary on the right $\ge height[left]$. Thus, the right side will NEVER limit water below $leftMax$, allowing us to safely calculate water using $leftMax$ alone!


---

## 9. Tags & Related Problems

- **Tags**: `Two Pointers`, `Stack`, `LeetCode-42`, `Hard`
- **Related problems to practice next**:
- **Largest Rectangle in Histogram**: Histogram area.
