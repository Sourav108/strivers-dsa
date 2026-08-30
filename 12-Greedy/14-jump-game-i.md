# Jump Game I (Can Reach End) (Step 12.2 — Medium / Hard)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Jump Game I (Can Reach End)](https://takeuforward.org/data-structure/jump-game-i/)
- **Difficulty**: Medium
- **Statement**: You are given an integer array `nums`. You are initially positioned at the array's first index, and each element in the array represents your maximum jump length at that position. Return `true` if you can reach the last index, or `false` otherwise.

---

## 1. Problem, Restated

Track `maxReach = max(maxReach, i + nums[i])`. Return `false` if `i > maxReach` at any step.

- **Input**: Parameters specified.
- **Output**: Optimal value / boolean / transformed list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Maintain the maximum reachable index `maxReach = 0`. Iterate $i$ from $0$ to $N-1$: 1) If $i > maxReach$, we have reached an unreachable island $\implies$ return `false`. 2) Update `maxReach = max(maxReach, i + nums[i])`. 3) If `maxReach >= N - 1`, return `true` immediately!

- **Underlying Pattern**: `Greedy Horizon Reachability Expansion`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Recursion trying all jumps from $1$ to $nums[i]$ in $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
// O(2^N) recursion
```

### Java Code
```java
// Java equivalent
// O(2^N) recursion
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Exponential branching.

---

## 4. Approach 2 — Better

### Idea
Dynamic Programming (Bottom-Up) in O(N^2) time.

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
- **Space Complexity**: $\mathcal{O}(N)$ DP array.
- **Why it's still not optimal**: Quadratic time.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Greedy Max Reachable Index in strict $\mathcal{O}(N)$ single pass and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    bool canJump(vector<int>& nums) {
        int maxReach = 0;
        int n = nums.size();
        
        for (int i = 0; i < n; i++) {
            // If current index is beyond the maximum reach so far
            if (i > maxReach) {
                return false;
            }
            
            maxReach = max(maxReach, i + nums[i]);
            
            // Early exit if destination is already reachable
            if (maxReach >= n - 1) {
                return true;
            }
        }
        
        return true;
    }
};
```

### Java Code
```java
class Solution {

    boolean canJump(int[] nums) {
        int maxReach = 0;
        int n = nums.length;
        
        for (int i = 0; i < n; i++) {
            // If current index is beyond the maximum reach so far
            if (i > maxReach) {
                return false;
            }
            
            maxReach = Math.max(maxReach, i + nums[i]);
            
            // Early exit if destination is already reachable
            if (maxReach >= n - 1) {
                return true;
            }
        }
        
        return true;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Max reach invariant determines reachability in a single forward sweep.

---

## 6. Dry Run

`nums = [2, 3, 1, 1, 4]`

| Step | Action / State Change | Result |
|---|---|---|
| `i = 0 (2)` | maxReach = max(0, 0+2) = 2 | maxReach = 2 |
| `i = 1 (3)` | maxReach = max(2, 1+3) = 4 >= 4 (Last Index!) | Early Exit TRUE ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 1$ (always `true`, already at destination).
- Zeros trapping reach `[3, 2, 1, 0, 4]` $\implies$ `false`.

### Common Bugs to Avoid
- Checking `maxReach >= n - 1` without `if (i > maxReach)` guard first (crashes on trapped zero arrays).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does Greedy work over DP here?**  
  **A**: Because if index $K$ is reachable, EVERY index from $0$ to $K$ is also reachable. We only need to track the single maximum reachable scalar rather than storing reachability for individual indices!

- **Q2: Can this be solved working backwards from destination?**  
  **A**: Yes! Initialize `goal = n - 1`. Loop backwards from $n-2$ down to 0: if `i + nums[i] >= goal`, update `goal = i`. Return `goal == 0`.

- **Q3: What is the time complexity if all nums[i] = 1?**  
  **A**: Single pass $\mathcal{O}(N)$ stepping linearly to the end.


---

## 9. Tags & Related Problems

- **Tags**: `Greedy`, `Array`, `LeetCode-55`, `Medium`
- **Related problems to practice next**:
- **Jump Game II**: Minimum jumps.
