# Jump Game II (Minimum Jumps to Reach End) (Step 12.2 — Medium / Hard)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Jump Game II (Minimum Jumps to Reach End)](https://takeuforward.org/data-structure/jump-game-ii/)
- **Difficulty**: Medium
- **Statement**: Given a 0-indexed array of integers `nums` of length $n$. You are initially positioned at `nums[0]`. Return the minimum number of jumps to reach `nums[n - 1]` (guaranteed reachable).

---

## 1. Problem, Restated

BFS Level-by-Level Greedy Horizon Range: count jumps when reaching current interval boundary `curEnd`.

- **Input**: Parameters specified.
- **Output**: Optimal value / boolean / transformed list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Think of this as **BFS on array indices**. At current jump, our current level window ends at `curEnd`. Track `farthest = max(farthest, i + nums[i])`. Loop $i$ from $0$ to $N - 2$: 1) `farthest = max(farthest, i + nums[i])`. 2) When $i == curEnd$, we MUST make a jump $\implies$ `jumps++`, `curEnd = farthest`. Return `jumps` in strict $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space!

- **Underlying Pattern**: `Implicit BFS Level Interval Jumps (`[curStart, curEnd]` $\to$ `[curEnd + 1, farthest]`)`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Dynamic Programming `dp[i] = 1 + min(dp[i+j])` in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
// DP O(N^2) approach
```

### Java Code
```java
// Java equivalent
// DP O(N^2) approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ DP table.
- **Why it's not good enough**: Quadratic time.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard greedy strategy below directly achieves optimal $\mathcal{O}(N \log N)$ or $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Implicit BFS Greedy Range in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int jump(vector<int>& nums) {
        int n = nums.size();
        if (n <= 1) return 0;
        
        int jumps = 0;
        int curEnd = 0;
        int farthest = 0;
        
        // Loop up to n - 2 (we don't need to jump from the last index)
        for (int i = 0; i < n - 1; i++) {
            farthest = max(farthest, i + nums[i]);
            
            // Reached the end of current jump range
            if (i == curEnd) {
                jumps++;
                curEnd = farthest;
                
                if (curEnd >= n - 1) break; // reached destination
            }
        }
        
        return jumps;
    }
};
```

### Java Code
```java
class Solution {

    int jump(int[] nums) {
        int n = nums.length;
        if (n <= 1) return 0;
        
        int jumps = 0;
        int curEnd = 0;
        int farthest = 0;
        
        // Loop up to n - 2 (we don't need to jump from the last index)
        for (int i = 0; i < n - 1; i++) {
            farthest = Math.max(farthest, i + nums[i]);
            
            // Reached the end of current jump range
            if (i == curEnd) {
                jumps++;
                curEnd = farthest;
                
                if (curEnd >= n - 1) break; // reached destination
            }
        }
        
        return jumps;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: BFS range grouping determines minimum jump milestones in a single forward pass.

---

## 6. Dry Run

`nums = [2, 3, 1, 1, 4]`

| Step | Action / State Change | Result |
|---|---|---|
| `i = 0 (2)` | farthest = max(0, 0+2) = 2. i == curEnd(0) -> jumps=1, curEnd=2 | jumps = 1, curEnd = 2 |
| `i = 1 (3)` | farthest = max(2, 1+3) = 4 | farthest = 4 |
| `i = 2 (1)` | farthest = max(4, 2+1) = 4. i == curEnd(2) -> jumps=2, curEnd=4 | jumps = 2, curEnd = 4 >= 4 |
| `Result` | Minimum jumps to reach end | `2` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 1$ (returns 0 jumps).
- Single jump covers whole array `[5, 1, 1, 1, 1]` $\implies 1$.

### Common Bugs to Avoid
- Looping up to $N - 1$ instead of $N - 2$ (triggers an extra redundant jump count when reaching the destination index!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does the loop run up to n - 2 instead of n - 1?**  
  **A**: Because once we reach index $n - 1$, we are ALREADY at our destination! If we looped to $n - 1$ and `i == curEnd`, we would incorrectly increment `jumps++` for an unnecessary jump.

- **Q2: Why is this algorithm equivalent to BFS?**  
  **A**: Each increment of `jumps` corresponds to moving to the next level in a BFS tree. The interval `[curStart, curEnd]` represents all nodes at distance $D$, and `farthest` represents the boundary of distance $D+1$.

- **Q3: How does Jump Game II differ from Jump Game I?**  
  **A**: Jump Game I only asks for BOOLEAN reachability (can we reach the end?). Jump Game II asks for the MINIMUM jumps (the shortest path length in the implicit jump graph).


---

## 9. Tags & Related Problems

- **Tags**: `Greedy`, `BFS`, `Array`, `LeetCode-45`, `Medium`
- **Related problems to practice next**:
- **Jump Game I**: Reachability check.
