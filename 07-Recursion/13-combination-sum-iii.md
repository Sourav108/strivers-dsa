# Combination Sum III (K numbers sum to N) (Step 7.2 — Subsequences Pattern)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Combination Sum III (K numbers sum to N)](https://takeuforward.org/recursion/combination-sum-iii/)
- **Difficulty**: Medium
- **Statement**: Find all valid combinations of $k$ numbers that sum up to $n$ such that: only numbers from $1$ to $9$ are used, each number is used at most once, and list contains no duplicates.

---

## 1. Problem, Restated

Find subsets of size $k$ from digits $1..9$ that sum to $n$.

- **Input**: Parameters specified.
- **Output**: Value or vector of combinations meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Loop from `startDigit` to 9. If `ds.size() == k` and `n == 0`, push to results. If `ds.size() > k` or `digit > n`, break early. Recurse on `digit + 1` with `n - digit`.

- **Underlying Pattern**: `Size-Bounded Backtracking ($1..9$ Digit Space)`.

---

## 3. Approach 1 — Naive / Pitfall

### Idea
Generate all $2^9 = 512$ subsets of digits $1..9$ and filter.

### C++17 Code
```cpp
// 512 subsets filter
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^9) = \mathcal{O}(1)$.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Brute force works, but pruned backtracking is clean.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic recursion/backtracking below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Digit Backtracking with Size and Sum Pruning.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
private:
    void backtrack(int start, int k, int n, vector<int>& current, vector<vector<int>>& result) {
        if ((int)current.size() == k) {
            if (n == 0) {
                result.push_back(current);
            }
            return;
        }
        
        for (int digit = start; digit <= 9; digit++) {
            if (digit > n) break; // early pruning
            
            current.push_back(digit);
            backtrack(digit + 1, k, n - digit, current, result);
            current.pop_back(); // backtrack
        }
    }

public:
    vector<vector<int>> combinationSum3(int k, int n) {
        vector<vector<int>> result;
        vector<int> current;
        backtrack(1, k, n, current, result);
        return result;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\binom{9}{k} \cdot k)$ bounded ($< 126$ states for $\binom{9}{4} = 126$).
- **Space Complexity**: $\mathcal{O}(k)$ recursion stack space.
- **Why this is optimal**: Fixed search space of 9 digits with immediate pruning.

---

## 6. Dry Run

$k = 3, n = 7$

| Step | Action / State Change | Result |
|---|---|---|
| `Start digit 1` | current = `[1, 2, 4]`, sum = 7 | Valid combination! |
| `Result` | Single valid combination | `[[1, 2, 4]]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $n > 45$ (sum of $1..9 = 45 \implies$ returns `[]`).
- $n < k$ (impossible $\implies$ `[]`).

### Common Bugs to Avoid
- Forgetting `digit + 1` in recursive call (allows duplicate digit reuse).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is the maximum number of combinations possible?**  
  **A**: Max combinations occurs at $\binom{9}{4} = \binom{9}{5} = 126$. The search tree is tiny ($\le 126$ leaves).


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Backtracking`, `LeetCode-216`, `Medium`
- **Related problems to practice next**:
- **Combination Sum I & II**: Generalized combinations.
