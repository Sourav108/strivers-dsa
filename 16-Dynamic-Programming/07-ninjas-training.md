# Ninja's Training (2D DP on Activities) (Step 16.1 — Introduction to DP)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Ninja's Training (2D DP on Activities)](https://takeuforward.org/data-structure/dynamic-programming-ninjas-training-dp-7/)
- **Difficulty**: Medium
- **Statement**: A Ninja is planning this $N$ days-long training schedule. Each day the Ninja can perform any one of three activities: Running (0), Fighting (1), or Learning Practice (2). Each activity on day $i$ gives merit points `points[i][task]`. The Ninja cannot perform the same activity on two consecutive days. Find the maximum merit points the Ninja can earn over $N$ days.

---

## 1. Problem, Restated

Find the maximum sum path through an $N \times 3$ matrix such that no two vertically adjacent selections share the same column index in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / Count result.
- **Complexity Goal**: Optimal $\mathcal{O}(M \times N)$ time and $\mathcal{O}(N)$ or $\mathcal{O}(1)$ auxiliary space.

---

## 2. Intuition & Pattern

**State Representation**: Let $\text{dp}[\text{day}][\text{last}]$ represent the maximum merit points from day 0 to `day` given that on `day`, the Ninja performed activity `last` (where `last` $\in \{0, 1, 2\}$, and $3$ denotes no restriction on day 0). 
**Transition Relation**: 
$$\text{dp}[\text{day}][\text{last}] = \max_{0 \le \text{task} \le 2, \text{task} \ne \text{last}} (\text{points}[\text{day}][\text{task}] + \text{dp}[\text{day} - 1][\text{task}])$$ 
**Base Case (Day 0)**: 
For each `last` $\in \{0, 1, 2, 3\}$: $\text{dp}[0][\text{last}] = \max_{\text{task} \ne \text{last}} (\text{points}[0][\text{task}])$. 
**Space Optimization**: Since day $i$ only depends on day $i - 1$, maintain a 4-element vector `prev(4)` in $\mathcal{O}(N \times 4 \times 3) = \mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

- **Underlying Pattern**: `2D DP with State Constraints / Last Task Elimination`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively explore all $3 \times 2^{N-1}$ valid task schedules in $\mathcal{O}(3 \times 2^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    int solve(int day, int last, const vector<vector<int>>& p) {
        if (day == 0) {
            int maxi = 0;
            for (int t = 0; t < 3; t++) if (t != last) maxi = max(maxi, p[0][t]);
            return maxi;
        }
        int maxi = 0;
        for (int t = 0; t < 3; t++) {
            if (t != last) maxi = max(maxi, p[day][t] + solve(day - 1, t, p));
        }
        return maxi;
    }
public:
    int ninjaTraining(int n, vector<vector<int>>& points) {
        return solve(n - 1, 3, points);
    }
};
```

### Java Code
```java
class SolutionNaive {
    int solve(int day, int last, int[][] p) {
        if (day == 0) {
            int maxi = 0;
            for (int t = 0; t < 3; t++) if (t != last) maxi = Math.max(maxi, p[0][t]);
            return maxi;
        }
        int maxi = 0;
        for (int t = 0; t < 3; t++) {
            if (t != last) maxi = Math.max(maxi, p[day][t] + solve(day - 1, t, p));
        }
        return maxi;
    }

    int ninjaTraining(int n, int[][] points) {
        return solve(n - 1, 3, points);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(3 \times 2^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion stack.
- **Why it's not good enough**: Exponential overlapping activity tree.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
Tabulation DP table of size N x 4 in O(N) time and O(N) space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class SolutionTabulation {
public:
    int ninjaTraining(int n, vector<vector<int>>& points) {
        vector<vector<int>> dp(n, vector<int>(4, 0));
        dp[0][0] = max(points[0][1], points[0][2]);
        dp[0][1] = max(points[0][0], points[0][2]);
        dp[0][2] = max(points[0][0], points[0][1]);
        dp[0][3] = max({points[0][0], points[0][1], points[0][2]});
        
        for (int day = 1; day < n; day++) {
            for (int last = 0; last < 4; last++) {
                for (int task = 0; task < 3; task++) {
                    if (task != last) {
                        dp[day][last] = max(dp[day][last], points[day][task] + dp[day - 1][task]);
                    }
                }
            }
        }
        return dp[n - 1][3];
    }
};
```

### Java Code
```java
class SolutionTabulation {

    int ninjaTraining(int n, int[][] points) {
        int[][] dp = new int[n][4];
        dp[0][0] = Math.max(points[0][1], points[0][2]);
        dp[0][1] = Math.max(points[0][0], points[0][2]);
        dp[0][2] = Math.max(points[0][0], points[0][1]);
        dp[0][3] = Math.max({points[0][0], points[0][1], points[0][2]});
        
        for (int day = 1; day < n; day++) {
            for (int last = 0; last < 4; last++) {
                for (int task = 0; task < 3; task++) {
                    if (task != last) {
                        dp[day][last] = Math.max(dp[day][last], points[day][task] + dp[day - 1][task]);
                    }
                }
            }
        }
        return dp[n - 1][3];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N \times 4)$ table space.
- **Why it's still not optimal**: Retaining all $N$ days of history is unnecessary.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
Space-Optimized 1D Buffer of size 4 in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int ninjaTraining(int n, vector<vector<int>>& points) {
        // prev[last] stores max points up to previous day given activity 'last' was chosen
        vector<int> prev(4, 0);
        
        // Base case for Day 0
        prev[0] = max(points[0][1], points[0][2]);
        prev[1] = max(points[0][0], points[0][2]);
        prev[2] = max(points[0][0], points[0][1]);
        prev[3] = max({points[0][0], points[0][1], points[0][2]});
        
        for (int day = 1; day < n; day++) {
            vector<int> cur(4, 0);
            
            for (int last = 0; last < 4; last++) {
                for (int task = 0; task < 3; task++) {
                    if (task != last) {
                        cur[last] = max(cur[last], points[day][task] + prev[task]);
                    }
                }
            }
            
            prev = cur; // Roll state forward
        }
        
        // Return max points on the last day with no restriction (last = 3)
        return prev[3];
    }
};
```

### Java Code
```java
class Solution {

    int ninjaTraining(int n, int[][] points) {
        // prev[last] stores max points up to previous day given activity 'last' was chosen
        int[] prev = new int[4];
        
        // Base case for Day 0
        prev[0] = Math.max(points[0][1], points[0][2]);
        prev[1] = Math.max(points[0][0], points[0][2]);
        prev[2] = Math.max(points[0][0], points[0][1]);
        prev[3] = Math.max({points[0][0], points[0][1], points[0][2]});
        
        for (int day = 1; day < n; day++) {
            int[] cur = new int[4];
            
            for (int last = 0; last < 4; last++) {
                for (int task = 0; task < 3; task++) {
                    if (task != last) {
                        cur[last] = Math.max(cur[last], points[day][task] + prev[task]);
                    }
                }
            }
            
            prev = cur; // Roll state forward
        }
        
        // Return max points on the last day with no restriction (last = 3)
        return prev[3];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time ($N$ days $\times 4 \times 3 = 12N$ operations).
- **Space Complexity**: $\mathcal{O}(1)$ space (two 4-element vectors `prev` and `cur`).
- **Why this is optimal**: Rolling 4 state values achieves minimum possible cache footprint and zero dynamic memory allocations.

---

## 6. Dry Run

`points = [[10, 40, 70], [20, 50, 80], [30, 60, 90]]` ($N = 3$)

| Step | Action / State Change | Result |
|---|---|---|
| `Day 0 Base` | prev[0]=max(40,70)=70, prev[1]=max(10,70)=70, prev[2]=max(10,40)=40, prev[3]=70 | Day 0 ready |
| `Day 1 (20, 50, 80)` | cur[0]=max(50+70, 80+40)=120; cur[1]=max(20+70, 80+40)=120; cur[2]=max(20+70, 50+70)=120; cur[3]=120 | prev = [120, 120, 120, 120] |
| `Day 2 (30, 60, 90)` | cur[3] = max(30+120, 60+120, 90+120) = 210 | cur[3] = 210 |
| `Result` | Return 210 (Day 0: 70, Day 1: 50, Day 2: 90 $\implies 70 + 50 + 90 = 210$) | Max Points = 210 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 1$ (returns $\max(\text{points}[0])$).
- All points 0.
- One activity strictly dominates every day (alternation required).

### Common Bugs to Avoid
- Forgetting `task != last` check.
- Setting `cur` directly to `prev` without recomputing all 4 cases.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is the state space size 4 instead of 3 for the 'last' parameter?**  
  **A**: Values $0, 1, 2$ represent that activity $0, 1, 2$ was performed yesterday. The 4th state `last = 3` represents 'no task performed yet / no constraint', which allows us to cleanly start at day $N-1$ and query the global maximum answer via `dp[n-1][3]` without extra max loops!

- **Q2: How to generalize this to K activities instead of 3 (Paint House II / LeetCode 265)?**  
  **A**: For $K$ colors/activities, brute transition is $\mathcal{O}(N \times K^2)$. We can optimize to $\mathcal{O}(N \times K)$ by maintaining the **minimum and second minimum** values of the previous row along with the index of the minimum!

- **Q3: How to print the chosen sequence of activities across the N days?**  
  **A**: Maintain a 2D choice matrix `bestChoice[day][last]`. Reconstruct path backward from day $N-1$ down to 0.


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `2D DP`, `State Machine`, `Space Optimization`, `Medium`
- **Related problems to practice next**:
- **Maximum Sum Non-Adjacent**: 1D state exclusion.
- **Grid Unique Paths**: 2D grid transitions.
