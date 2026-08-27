# Minimum Cost to Cut a Stick (Step 16.7 — Matrix Chain Multiplication / Partition DP)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Minimum Cost to Cut a Stick](https://takeuforward.org/data-structure/minimum-cost-to-cut-the-stick-dp-50/)
- **Difficulty**: Hard
- **Statement**: Given an integer `n` representing a wooden stick of length `n`, and an integer array `cuts` where `cuts[i]` denotes a position you should perform a cut at. You should perform all the cuts in any order. The cost of one cut is the length of the stick to be cut. Return the minimum total cost to make all the cuts.

---

## 1. Problem, Restated

Find the optimal order of cuts to minimize total cutting cost using Sorted Boundary Array + Partition DP in $\mathcal{O}(C^3)$ time and $\mathcal{O}(C^2)$ space (where $C = \text{cuts.size()}$).

- **Input**: Problem constraints and parameters.
- **Output**: Length / Count / Cost result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**Why Standard Greedy Fails**: 
Cutting the stick at position $k$ divides the stick into two independent sub-sticks: $[i, k]$ and $[k, j]$. The cost of this cut is the current stick length: $\text{cuts}[j] - \text{cuts}[i]$. 
Subsequent cuts on the left stick $[i, k]$ NEVER affect the right stick $[k, j]$! 
**Algorithm**: 
1) Pad the `cuts` array with endpoints: insert `0` at the beginning and `n` at the end: `cuts = [0, c1, c2, ..., n]`. 
2) **Sort `cuts` in ascending order**: Sorting ensures that sub-problems are cleanly bounded by indices `cuts[i]` and `cuts[j]`. 
3) **Partition DP**: 
   $$\text{dp}[i][j] = (\text{cuts}[j] - \text{cuts}[i]) + \min_{i < k < j} (\text{dp}[i][k] + \text{dp}[k][j])$$ 
4) Base Case: If $i + 1 == j$ (no cuts between $i$ and $j$), cost is 0. 
Runs in $\mathcal{O}(C^3)$ time and $\mathcal{O}(C^2)$ space (where $C \le 100$).

- **Underlying Pattern**: `MCM Partition DP on Cut Boundaries (`cuts = [0, ..., n]`)`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively test all $C!$ permutations of cuts in $\mathcal{O}(C!)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    // O(C!) factorial permutation search
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(C!)$ time.
- **Space Complexity**: $\mathcal{O}(C)$ stack.
- **Why it's not good enough**: Factorial search over all cut orders.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
Top-Down Memoization DP table of size (C+2) x (C+2) in O(C^3) time and O(C^2) space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class SolutionMemo {
    int memo(int i, int j, const vector<int>& cuts, vector<vector<int>>& dp) {
        if (i + 1 >= j) return 0;
        if (dp[i][j] != -1) return dp[i][j];
        
        int minCost = 1e9;
        for (int k = i + 1; k < j; k++) {
            int cost = (cuts[j] - cuts[i]) + memo(i, k, cuts, dp) + memo(k, j, cuts, dp);
            minCost = min(minCost, cost);
        }
        return dp[i][j] = minCost;
    }
public:
    int minCost(int n, vector<int>& cuts) {
        cuts.push_back(0);
        cuts.push_back(n);
        sort(cuts.begin(), cuts.end());
        int m = cuts.size();
        vector<vector<int>> dp(m, vector<int>(m, -1));
        return memo(0, m - 1, cuts, dp);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(C^3)$ time.
- **Space Complexity**: $\mathcal{O}(C^2)$ space.
- **Why it's still not optimal**: Recursion stack frames.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Sorted Boundary Array + Bottom-Up Interval DP in $\mathcal{O}(C^3)$ time and $\mathcal{O}(C^2)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int minCost(int n, vector<int>& cuts) {
        // Step 1: Add boundary endpoints 0 and n, and sort
        cuts.push_back(0);
        cuts.push_back(n);
        sort(cuts.begin(), cuts.end());
        
        int m = cuts.size(); // Total points = C + 2
        
        // dp[i][j] stores min cost to make all cuts between cuts[i] and cuts[j]
        vector<vector<int>> dp(m, vector<int>(m, 0));
        
        // Step 2: Iterate chain length from 2 to m - 1
        for (int len = 2; len < m; len++) {
            for (int i = 0; i < m - len; i++) {
                int j = i + len;
                dp[i][j] = 1e9;
                
                // Try making cut at index k first
                for (int k = i + 1; k < j; k++) {
                    int cost = (cuts[j] - cuts[i]) + dp[i][k] + dp[k][j];
                    dp[i][j] = min(dp[i][j], cost);
                }
            }
        }
        
        // Entire stick between 0 (cuts[0]) and n (cuts[m - 1])
        return dp[0][m - 1];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(C^3)$ time (where $C = \text{cuts.size()} \le 100$, $C^3 \approx 10^6$ operations $\approx 5$ ms).
- **Space Complexity**: $\mathcal{O}(C^2)$ space (DP matrix of size $(C + 2) \times (C + 2)$).
- **Why this is optimal**: Sorting bounds sub-problems cleanly, enabling exact MCM partition minimization.

---

## 6. Dry Run

`n = 7`, `cuts = [1, 3, 4, 5]`, Padded & Sorted: `[0, 1, 3, 4, 5, 7]` ($m = 6$)

| Step | Action / State Change | Result |
|---|---|---|
| `len = 2` | No cuts between adjacent points $\implies$ all dp[i][i+1] = 0 | Base ready |
| `len = 3 (e.g. [0, 3])` | i=0, j=2 (pts 0, 3): cut at k=1 (pt 1) $\implies cost = (3 - 0) + 0 + 0 = 3$ | dp[0][2] = 3 |
| `len = 3 (e.g. [1, 4])` | i=1, j=3 (pts 1, 4): cut at k=2 (pt 3) $\implies cost = (4 - 1) + 0 + 0 = 3$ | dp[1][3] = 3 |
| `Optimal Order` | Cut at 3 (cost 7) $\implies$ pieces `[0, 3]` and `[3, 7]`; then cut 1, 4, 5 $\implies 7 + 3 + 4 + 2 = 16$ | Cost = 16 |
| `Result` | Return dp[0][5] = 16 | Min Cost = 16 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single cut $C = 1$ (returns $n$).
- Cuts at consecutive points `[1, 2, 3]`.
- Large stick length $n = 10^6$ with small $C = 100$.

### Common Bugs to Avoid
- Forgetting to append `0` and `n` to `cuts`.
- Forgetting `sort(cuts.begin(), cuts.end())` (without sorting, `cuts[j] - cuts[i]` gives wrong stick length!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is sorting the 'cuts' array essential for this DP?**  
  **A**: Because cutting at point $k$ splits the stick into left interval $[cuts[i], cuts[k]]$ and right interval $[cuts[k], cuts[j]]$. Only when `cuts` is sorted does every cut index $k \in (i, j)$ physically lie inside the segment $(cuts[i], cuts[j])$!

- **Q2: Why is the time complexity independent of n (length of the stick)?**  
  **A**: Because the DP state space is defined entirely by the INDICES in the `cuts` array ($C \le 100$), NOT the value of $n$ ($n \le 10^6$)! This makes runtime $\mathcal{O}(C^3)$, which is extremely fast even for $n = 10^9$!

- **Q3: How does Cut a Stick compare to Burst Balloons (Problem 51)?**  
  **A**: Both are Partition DP problems. In Cut a Stick, we pick the FIRST cut to make (dividing stick length $cuts[j] - cuts[i]$). In Burst Balloons, we pick the LAST balloon to burst (multiplying $nums[i-1] \times nums[k] \times nums[j+1]$)!


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `MCM`, `Partition DP`, `LeetCode-1547`, `Hard`
- **Related problems to practice next**:
- **Matrix Chain Multiplication**: MCM template.
- **Burst Balloons**: Last operation partition DP.
