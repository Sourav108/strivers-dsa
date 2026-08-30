# Burst Balloons (Partition DP) (Step 16.7 — Matrix Chain Multiplication / Partition DP)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Burst Balloons (Partition DP)](https://takeuforward.org/data-structure/burst-balloons-partition-dp-dp-51/)
- **Difficulty**: Hard
- **Statement**: You are given $n$ balloons, indexed from 0 to $n - 1$. Each balloon is painted with a number on it represented by an array `nums`. You are asked to burst all the balloons. If you burst the $i$-th balloon, you will get $\text{nums}[i - 1] \times \text{nums}[i] \times \text{nums}[i + 1]$ coins. If $i - 1$ or $i + 1$ goes out of bounds of the array, then treat it as if there is a balloon with a 1 painted on it. Return the maximum coins you can collect by bursting the balloons wisely.

---

## 1. Problem, Restated

Maximize coins by determining the optimal balloon bursting sequence using Bottom-Up 'Last Balloon Burst' Partition DP in $\mathcal{O}(N^3)$ time and $\mathcal{O}(N^2)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Value / Count result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**The Reverse Thinking Breakthrough**: 
- If we think about which balloon to burst FIRST, bursting balloon $k$ makes its left neighbor and right neighbor become adjacent, creating dependent subproblems! 
- **Think in REVERSE: Which balloon is burst LAST in the interval $[i, j]$?** 
If balloon $k \in [i, j]$ is the VERY LAST balloon to be burst in $[i, j]$: 
1) All other balloons in $[i, k-1]$ and $[k+1, j]$ have ALREADY been burst! 
2) Therefore, balloon $k$'s immediate surviving neighbors at the moment it bursts are **GUARANTEED to be `nums[i - 1]` and `nums[j + 1]`**! 
3) Subproblems $[i, k-1]$ and $[k+1, j]$ become **100% INDEPENDENT**! 
**Master Recurrence**: 
$$\text{dp}[i][j] = \max_{i \le k \le j} \left( \text{dp}[i][k - 1] + \text{dp}[k + 1][j] + \text{nums}[i - 1] \times \text{nums}[k] \times \text{nums}[j + 1] \right)$$ 
**Padded Array**: Insert 1 at beginning and end: `[1, nums[0], ..., nums[n-1], 1]`. Runs in $\mathcal{O}(N^3)$ time and $\mathcal{O}(N^2)$ space.

- **Underlying Pattern**: `Reverse Partition DP / Optimal 'Last Balloon Burst' Interval Substructure`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively test all $N!$ balloon bursting permutations in $\mathcal{O}(N!)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    // O(N!) permutation search
};
```

### Java Code
```java
class SolutionNaive {
    // O(N!) permutation search
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N!)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion stack.
- **Why it's not good enough**: Factorial search over all balloon bursting permutations.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
Top-Down Memoization DP table of size (N+2) x (N+2) in O(N^3) time and O(N^2) space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class SolutionMemo {
    int memo(int i, int j, const vector<int>& a, vector<vector<int>>& dp) {
        if (i > j) return 0;
        if (dp[i][j] != -1) return dp[i][j];
        int maxCoins = 0;
        for (int k = i; k <= j; k++) {
            int coins = a[i - 1] * a[k] * a[j + 1] + memo(i, k - 1, a, dp) + memo(k + 1, j, a, dp);
            maxCoins = max(maxCoins, coins);
        }
        return dp[i][j] = maxCoins;
    }
public:
    int maxCoins(vector<int>& nums) {
        int n = nums.size();
        nums.insert(nums.begin(), 1);
        nums.push_back(1);
        vector<vector<int>> dp(n + 2, vector<int>(n + 2, -1));
        return memo(1, n, nums, dp);
    }
};
```

### Java Code
```java
class SolutionMemo {
    int memo(int i, int j, int[] a, int[][] dp) {
        if (i > j) return 0;
        if (dp[i][j] != -1) return dp[i][j];
        int maxCoins = 0;
        for (int k = i; k <= j; k++) {
            int coins = a[i - 1] * a[k] * a[j + 1] + memo(i, k - 1, a, dp) + memo(k + 1, j, a, dp);
            maxCoins = Math.max(maxCoins, coins);
        }
        return dp[i][j] = maxCoins;
    }

    int maxCoins(int[] nums) {
        int n = nums.length;
        nums.add(nums.begin(), 1);
        nums.add(1);
        int[][] dp = new int[n + 2][n + 2];
        return memo(1, n, nums, dp);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^3)$ time.
- **Space Complexity**: $\mathcal{O}(N^2)$ space.
- **Why it's still not optimal**: Recursion stack frames.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Bottom-Up Interval Length Partition DP in $\mathcal{O}(N^3)$ time and $\mathcal{O}(N^2)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int maxCoins(vector<int>& nums) {
        int n = nums.size();
        
        // Pad with 1s at both boundaries
        vector<int> a(n + 2, 1);
        for (int i = 0; i < n; i++) {
            a[i + 1] = nums[i];
        }
        
        // dp[i][j] stores max coins from bursting all balloons in interval [i ... j]
        vector<vector<int>> dp(n + 2, vector<int>(n + 2, 0));
        
        // Bottom-up iteration from bottom row to top (i from n down to 1)
        for (int i = n; i >= 1; i--) {
            for (int j = i; j <= n; j++) {
                int maxCoins = 0;
                
                // Balloon k is the LAST balloon to burst in interval [i ... j]
                for (int k = i; k <= j; k++) {
                    int coins = a[i - 1] * a[k] * a[j + 1] 
                              + dp[i][k - 1] 
                              + dp[k + 1][j];
                              
                    maxCoins = max(maxCoins, coins);
                }
                
                dp[i][j] = maxCoins;
            }
        }
        
        return dp[1][n];
    }
};
```

### Java Code
```java
class Solution {

    int maxCoins(int[] nums) {
        int n = nums.length;
        
        // Pad with 1s at both boundaries
        int[] a = new int[n + 2];
        for (int i = 0; i < n; i++) {
            a[i + 1] = nums[i];
        }
        
        // dp[i][j] stores max coins from bursting all balloons in interval [i ... j]
        int[][] dp = new int[n + 2][n + 2];
        
        // Bottom-up iteration from bottom row to top (i from n down to 1)
        for (int i = n; i >= 1; i--) {
            for (int j = i; j <= n; j++) {
                int maxCoins = 0;
                
                // Balloon k is the LAST balloon to burst in interval [i ... j]
                for (int k = i; k <= j; k++) {
                    int coins = a[i - 1] * a[k] * a[j + 1] 
                              + dp[i][k - 1] 
                              + dp[k + 1][j];
                              
                    maxCoins = Math.max(maxCoins, coins);
                }
                
                dp[i][j] = maxCoins;
            }
        }
        
        return dp[1][n];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^3)$ time ($\approx N^3 / 6$ state evaluations).
- **Space Complexity**: $\mathcal{O}(N^2)$ space (a 2D DP matrix of size $(N + 2) \times (N + 2)$).
- **Why this is optimal**: Reverse last-balloon framing decouples sub-problems into exact $\mathcal{O}(N^3)$ interval dynamic programming.

---

## 6. Dry Run

`nums = [3, 1, 5, 8]`, Padded: `a = [1, 3, 1, 5, 8, 1]` ($N = 4$)

| Step | Action / State Change | Result |
|---|---|---|
| `len = 1 (k=i=j)` | dp[1][1]=1*3*1=3; dp[2][2]=3*1*5=15; dp[3][3]=1*5*8=40; dp[4][4]=5*8*1=40 | Single balloon intervals |
| `len = 2` | dp[1][2]: k=1(1*3*5+dp[2][2]=30), k=2(1*1*5+dp[1][1]=8) $\implies 30$ | dp[1][2] = 30 |
| `len = 3` | dp[2][4]: k=2(3*1*1+dp[3][4]=43), k=3(3*5*1+dp[2][2]+dp[4][4]=70), k=4(3*8*1+dp[2][3]=156) $\implies 156$ | dp[2][4] = 156 |
| `len = 4: dp[1][4]` | k=3 (balloon 5 is LAST): $1 \times 5 \times 1 + dp[1][2] + dp[4][4] = 5 + 30 + 40 = 75$; k=4 (balloon 8 is LAST): $1 \times 8 \times 1 + dp[1][3] = 8 + 159 = 167$ | dp[1][4] = 167 |
| `Result` | Return dp[1][4] = 167 (Burst order: 1 -> 5 -> 3 -> 8) | Max Coins = 167 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 1$ (returns `nums[0]`).
- All balloons value 1 (returns $N$).
- Array with zeroes.

### Common Bugs to Avoid
- Thinking forward (burst first balloon) which creates non-independent overlapping boundaries.
- Using 0-indexed bounds without padding.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does thinking in REVERSE (which balloon bursts LAST) solve the dependency problem?**  
  **A**: If balloon $k$ bursts FIRST, its left and right neighbors merge, altering future boundaries dynamically. If balloon $k$ bursts LAST, all other balloons in $[i, j]$ are ALREADY GONE, so $k$'s neighbors are FIXED forever as $i-1$ and $j+1$! This makes $[i, k-1]$ and $[k+1, j]$ entirely independent!

- **Q2: Can we reconstruct the exact optimal balloon bursting order?**  
  **A**: Maintain `split[i][j] = k`. The last balloon to burst is $k$; recursively reconstruct bursting order for $[i, k-1]$ and $[k+1, j]$ to print the sequence in $\mathcal{O}(N)$ time!

- **Q3: How does Burst Balloons relate to Minimum Cost to Cut a Stick?**  
  **A**: Cut a Stick chooses the FIRST cut (cost $cuts[j] - cuts[i]$), while Burst Balloons chooses the LAST balloon (coins $a[i-1] \times a[k] \times a[j+1]$). Both are canonical MCM partition DP!


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `MCM`, `Partition DP`, `LeetCode-312`, `Hard`
- **Related problems to practice next**:
- **Minimum Cost to Cut a Stick**: First cut counterpart.
- **Matrix Chain Multiplication**: Base MCM template.
