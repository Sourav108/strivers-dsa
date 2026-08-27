# Evaluate Boolean Expression to True (Boolean Parenthesization) (Step 16.7 — Matrix Chain Multiplication / Partition DP)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Evaluate Boolean Expression to True (Boolean Parenthesization)](https://takeuforward.org/data-structure/evaluate-boolean-expression-to-true-partition-dp-dp-52/)
- **Difficulty**: Hard
- **Statement**: Given a boolean expression `exp` consisting of symbols 'T' (True), 'F' (False) and operators '&' (AND), '|' (OR), '^' (XOR). Find the number of ways to parenthesize the expression so that the value of the expression evaluates to True. Return the answer modulo $10^9 + 7$.

---

## 1. Problem, Restated

Count the number of ways to parenthesize a boolean expression to evaluate to True using 3D Partition DP $\text{dp}[i][j][\text{isTrue}]$ in $\mathcal{O}(N^3)$ time and $\mathcal{O}(N^2)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Value / Count result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**Why We Must Track BOTH True and False**: 
To evaluate boolean operators at pivot operator $k$: 
- `&` requires $(\text{True}, \text{True})$ 
- `|` requires $(\text{True}, \text{True})$, $(\text{True}, \text{False})$, $(\text{False}, \text{True})$ 
- `^` requires $(\text{True}, \text{False})$ or $(\text{False}, \text{True})$ 
Because operators like `^` and `|` depend on sub-expressions evaluating to FALSE, we MUST maintain two counts for every sub-interval $[i, j]$: 
1) $\text{dp}[i][j][1] =$ ways interval $[i, j]$ evaluates to **True** 
2) $\text{dp}[i][j][0] =$ ways interval $[i, j]$ evaluates to **False** 
**Transitions across operator $k \in [i+1, j-1]$ (step 2)**: 
- $LT = \text{solve}(i, k-1, 1)$, $LF = \text{solve}(i, k-1, 0)$ 
- $RT = \text{solve}(k+1, j, 1)$, $RF = \text{solve}(k+1, j, 0)$ 
Apply boolean truth tables for `&`, `|`, `^` modulo $10^9+7$ in $\mathcal{O}(N^3)$ time and $\mathcal{O}(N^2)$ space.

- **Underlying Pattern**: `Partition DP with Dual Boolean Truth States (`waysTrue` & `waysFalse`)`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively parenthesize all sub-expressions without memoization in $\mathcal{O}(4^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    // O(4^N) recursive boolean parenthesization
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(4^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ stack.
- **Why it's not good enough**: Combinatorial parenthesization branching.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard DP below directly achieves optimal bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
3D Top-Down Memoization Partition DP in $\mathcal{O}(N^3)$ time and $\mathcal{O}(N^2)$ space.

### C++17 Code
```cpp
#include <vector>
#include <string>
using namespace std;

class Solution {
    const int MOD = 1e9 + 7;
    
    long long solve(int i, int j, int isTrue, const string& exp, vector<vector<vector<long long>>>& dp) {
        // Base case 1: Invalid range
        if (i > j) return 0;
        
        // Base case 2: Single operand ('T' or 'F')
        if (i == j) {
            if (isTrue) return exp[i] == 'T' ? 1 : 0;
            else return exp[i] == 'F' ? 1 : 0;
        }
        
        if (dp[i][j][isTrue] != -1) return dp[i][j][isTrue];
        
        long long ways = 0;
        
        // Operator k is always at odd indices (i + 1, i + 3, ..., j - 1)
        for (int k = i + 1; k <= j - 1; k += 2) {
            long long lT = solve(i, k - 1, 1, exp, dp);
            long long lF = solve(i, k - 1, 0, exp, dp);
            long long rT = solve(k + 1, j, 1, exp, dp);
            long long rF = solve(k + 1, j, 0, exp, dp);
            
            char op = exp[k];
            
            if (op == '&') {
                if (isTrue) {
                    ways = (ways + (lT * rT) % MOD) % MOD;
                } else {
                    ways = (ways + (lF * rT)%MOD + (lT * rF)%MOD + (lF * rF)%MOD) % MOD;
                }
            } 
            else if (op == '|') {
                if (isTrue) {
                    ways = (ways + (lT * rT)%MOD + (lT * rF)%MOD + (lF * rT)%MOD) % MOD;
                } else {
                    ways = (ways + (lF * rF) % MOD) % MOD;
                }
            } 
            else if (op == '^') {
                if (isTrue) {
                    ways = (ways + (lT * rF)%MOD + (lF * rT)%MOD) % MOD;
                } else {
                    ways = (ways + (lT * rT)%MOD + (lF * rF)%MOD) % MOD;
                }
            }
        }
        
        return dp[i][j][isTrue] = ways;
    }
    
public:
    int countWays(int n, string exp) {
        vector<vector<vector<long long>>> dp(n, vector<vector<long long>>(n, vector<long long>(2, -1)));
        return solve(0, n - 1, 1, exp, dp);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^3)$ time ($\mathcal{O}(N^2 \times 2)$ states $\times \mathcal{O}(N)$ operator partitions).
- **Space Complexity**: $\mathcal{O}(N^2)$ space (DP table of size $N \times N \times 2$).
- **Why this is optimal**: Simultaneous evaluation of True and False ways resolves boolean operator combinations with zero redundancy.

---

## 6. Dry Run

`exp = "T|T&F"` ($N = 5$)

| Step | Action / State Change | Result |
|---|---|---|
| `Structure` | Operands at indices 0, 2, 4 (`'T'`, `'T'`, `'F'`); Operators at 1 (`'|'`), 3 (`'&'`') | Ready |
| `Partition k=1 ('|')` | Left: `'T'` (1 way True), Right: `'T&F'` (T&F = False $\implies$ 1 way False). True | False = True $\implies 1 \times 1 = 1$ way | Ways = 1 |
| `Partition k=3 ('&')` | Left: `'T|T'` (T|T = True $\implies$ 1 way True), Right: `'F'` (0 ways True). True & False = False | Ways = 0 |
| `Result` | Total ways evaluating to True = $1 + 0 = 1$ (`(T)|(T&F)`) | Count = 1 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single character `'T'` (returns 1).
- Single character `'F'` (returns 0).
- Expression always evaluating to False (returns 0).

### Common Bugs to Avoid
- Stepping operator loop by $k++$ instead of $k += 2$ (operators only occur at odd indices!).
- Integer overflow on `(lT * rT)` before modulo.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why MUST we step k by 2 in the partition loop?**  
  **A**: Because in a well-formed boolean expression `T|F&T`, operands are at even indices ($0, 2, 4$) and operators are strictly at odd indices ($1, 3, 5$). Partitioning at an operand index would produce malformed sub-expressions!

- **Q2: How does Boolean Parenthesization relate to Catalan Numbers?**  
  **A**: The total number of valid parenthesizations of $n$ boolean operands is the $(n-1)$-th Catalan Number $C_{n-1} = \frac{1}{n}\binom{2n-2}{n-1}$!

- **Q3: What if NOT (~) unary operator was also allowed?**  
  **A**: Unary NOT operator does not split the expression into two halves; it flips the boolean flag: `solve(i+1, j, !isTrue)` in $\mathcal{O}(N)$ transitions.


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `MCM`, `Partition DP`, `Boolean`, `Modulo Arithmetic`, `Hard`
- **Related problems to practice next**:
- **Matrix Chain Multiplication**: Parenthesization template.
- **Burst Balloons**: Partition DP.
