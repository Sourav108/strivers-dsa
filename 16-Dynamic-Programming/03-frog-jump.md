# Frog Jump (Geek Jump 1 or 2 steps) (Step 16.1 — Introduction to DP)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Frog Jump (Geek Jump 1 or 2 steps)](https://takeuforward.org/data-structure/dynamic-programming-frog-jump-dp-3/)
- **Difficulty**: Easy
- **Statement**: A frog is initially on the 1st stair (0-indexed). It wants to reach the $N$-th stair. The height of the $i$-th stair is `height[i]`. If the frog jumps from $i$-th to $j$-th stair, energy lost is $|\text{height}[i] - \text{height}[j]|$. If the frog can jump either 1 or 2 steps, find the minimum total energy used to reach the $(N-1)$-th stair.

---

## 1. Problem, Restated

Find the minimum energy path from index 0 to $n - 1$ where each jump of size 1 or 2 costs $|\text{height}[i] - \text{height}[j]|$ in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / Count result.
- **Complexity Goal**: Optimal $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ or $\mathcal{O}(K)$ auxiliary space.

---

## 2. Intuition & Pattern

To land on stair $i$, the frog must have jumped from either: 
1) Stair $i - 1$: Cost is $\text{dp}[i - 1] + |\text{height}[i] - \text{height}[i - 1]|$ 
2) Stair $i - 2$ (if $i > 1$): Cost is $\text{dp}[i - 2] + |\text{height}[i] - \text{height}[i - 2]|$ 
**Recurrence**: 
$$\text{dp}[i] = \min(\text{dp}[i - 1] + |\text{height}[i] - \text{height}[i - 1]|, \text{dp}[i - 2] + |\text{height}[i] - \text{height}[i - 2]|)$$ 
Base case: $\text{dp}[0] = 0$. Since $\text{dp}[i]$ depends only on $\text{dp}[i-1]$ and $\text{dp}[i-2]$, maintain two sliding variables `prev` and `prev2` in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

- **Underlying Pattern**: `1D DP / Min-Cost Transition with 2 Lookbacks`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively explore both 1-step and 2-step jumps from $n-1$ down to 0 in $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    int solve(int i, const vector<int>& h) {
        if (i == 0) return 0;
        int jumpOne = solve(i - 1, h) + abs(h[i] - h[i - 1]);
        int jumpTwo = 1e9;
        if (i > 1) jumpTwo = solve(i - 2, h) + abs(h[i] - h[i - 2]);
        return min(jumpOne, jumpTwo);
    }
public:
    int minimumEnergy(vector<int>& height, int n) {
        return solve(n - 1, height);
    }
};
```

### Java Code
```java
class SolutionNaive {
    int solve(int i, int[] h) {
        if (i == 0) return 0;
        int jumpOne = solve(i - 1, h) + Math.abs(h[i] - h[i - 1]);
        int jumpTwo = 1e9;
        if (i > 1) jumpTwo = solve(i - 2, h) + Math.abs(h[i] - h[i - 2]);
        return Math.min(jumpOne, jumpTwo);
    }

    int minimumEnergy(int[] height, int n) {
        return solve(n - 1, height);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion stack.
- **Why it's not good enough**: Overlapping subproblem branches recomputed exponentially.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
Bottom-Up Tabulation in O(N) time and O(N) space.

### C++17 Code
```cpp
#include <vector>
#include <cmath>
#include <algorithm>
using namespace std;

class SolutionTabulation {
public:
    int minimumEnergy(vector<int>& height, int n) {
        vector<int> dp(n, 0);
        dp[0] = 0;
        for (int i = 1; i < n; i++) {
            int fs = dp[i - 1] + abs(height[i] - height[i - 1]);
            int ss = 1e9;
            if (i > 1) ss = dp[i - 2] + abs(height[i] - height[i - 2]);
            dp[i] = min(fs, ss);
        }
        return dp[n - 1];
    }
};
```

### Java Code
```java
class SolutionTabulation {

    int minimumEnergy(int[] height, int n) {
        int[] dp = new int[n];
        dp[0] = 0;
        for (int i = 1; i < n; i++) {
            int fs = dp[i - 1] + Math.abs(height[i] - height[i - 1]);
            int ss = 1e9;
            if (i > 1) ss = dp[i - 2] + Math.abs(height[i] - height[i - 2]);
            dp[i] = Math.min(fs, ss);
        }
        return dp[n - 1];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ vector.
- **Why it's still not optimal**: Vector memory can be compressed to 2 variables.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
Space-Optimized DP in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <vector>
#include <cmath>
#include <algorithm>
using namespace std;

class Solution {
public:
    int minimumEnergy(vector<int>& height, int n) {
        int prev2 = 0; // dp[i - 2]
        int prev = 0;  // dp[i - 1]
        
        for (int i = 1; i < n; i++) {
            int jumpOne = prev + abs(height[i] - height[i - 1]);
            int jumpTwo = 1e9;
            if (i > 1) {
                jumpTwo = prev2 + abs(height[i] - height[i - 2]);
            }
            
            int cur = min(jumpOne, jumpTwo);
            prev2 = prev;
            prev = cur;
        }
        
        return prev;
    }
};
```

### Java Code
```java
class Solution {

    int minimumEnergy(int[] height, int n) {
        int prev2 = 0; // dp[i - 2]
        int prev = 0;  // dp[i - 1]
        
        for (int i = 1; i < n; i++) {
            int jumpOne = prev + Math.abs(height[i] - height[i - 1]);
            int jumpTwo = 1e9;
            if (i > 1) {
                jumpTwo = prev2 + Math.abs(height[i] - height[i - 2]);
            }
            
            int cur = Math.min(jumpOne, jumpTwo);
            prev2 = prev;
            prev = cur;
        }
        
        return prev;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time (single iteration over $N$ heights).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Constant memory usage with zero auxiliary allocations.

---

## 6. Dry Run

`height = [10, 20, 30, 10]`, $n = 4$

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | prev2 = 0, prev = 0 | i = 1 |
| `i = 1 (h=20)` | jump1 = $0 + |20 - 10| = 10$; jump2 = INF $\implies cur = 10$. prev2=0, prev=10 | dp[1] = 10 |
| `i = 2 (h=30)` | jump1 = $10 + |30 - 20| = 20$; jump2 = $0 + |30 - 10| = 20 \implies cur = 20$. prev2=10, prev=20 | dp[2] = 20 |
| `i = 3 (h=10)` | jump1 = $20 + |10 - 30| = 40$; jump2 = $10 + |10 - 20| = 20 \implies cur = 20$. prev2=20, prev=20 | dp[3] = 20 |
| `Result` | Return prev = 20 (Jump $0 \to 1 \to 3$: cost $|10-20| + |20-10| = 20$) | Min Energy = 20 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 1$ (returns 0).
- $N = 2$ (single jump only).
- All heights equal (energy = 0).

### Common Bugs to Avoid
- Accessing `height[i - 2]` when $i = 1$ without checking `if (i > 1)` (out of bounds underflow).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is greedy choice (always picking smaller height difference jump) sub-optimal?**  
  **A**: A greedy 1-step jump might incur cost 2 now, but force a huge cost 100 on the next step! A larger 2-step jump of cost 5 might bypass the expensive step entirely. Dynamic programming evaluates the global minimum over all paths!

- **Q2: How to print the actual sequence of stair indices the frog jumped on?**  
  **A**: Maintain a predecessor array `parent[i]` (recording whether $i-1$ or $i-2$ yielded the minimum). Backtrack from $n-1$ to 0 to reconstruct the jump sequence in $\mathcal{O}(N)$ time.

- **Q3: How does this generalize to Frog Jump with K Distances?**  
  **A**: Instead of checking 2 previous steps ($i-1, i-2$), loop over all $j \in [1, K]$: $\text{dp}[i] = \min_{1 \le j \le K}(\text{dp}[i - j] + |h[i] - h[i - j]|)$.


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `1D DP`, `Memoization`, `Tabulation`, `Easy`
- **Related problems to practice next**:
- **Frog Jump with K Distances**: K-step extension.
- **Climbing Stairs**: Unweighted predecessor.
