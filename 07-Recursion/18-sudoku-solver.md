# Sudoku Solver (Full grid validation) (Step 7.3 — Hard Recursion Problems & Backtracking)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Sudoku Solver (Full grid validation)](https://takeuforward.org/data-structure/sudoku-solver/)
- **Difficulty**: Hard
- **Statement**: Write a program to solve a Sudoku puzzle by filling the empty cells (marked with `'.'`) such that every row, column, and $3 \times 3$ sub-box contains the digits `1-9` exactly once.

---

## 1. Problem, Restated

Solve standard $9 \times 9$ Sudoku grid in-place using backtracking search.

- **Input**: Grid / String / Constraints.
- **Output**: Boolean feasibility or complete vector of solution configurations.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Traverse grid to find first empty cell `'.'`. Try placing digits `'1'` to `'9'`. Validate validity in $\mathcal{O}(9) = \mathcal{O}(1)$ time: check row $i$, column $j$, and $3 \times 3$ box at $(3(i/3) + k/3, 3(j/3) + k\%3)$. If valid: place digit, recurse `if (solve()) return true;`. If no digit works, reset `board[i][j] = '.'` and backtrack `return false;`.

- **Underlying Pattern**: `Grid Cell Search + 3-Way Rule Validation (`row`, `col`, `3x3 box`)`.

---

## 3. Approach 1 — Naive / Unpruned Search

### Idea
Full permutation of 81 cells without local constraint validation.

### C++17 Code
```cpp
// 9^81 unpruned search
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(9^{81})$ exponential.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Impossible runtime.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard backtracking algorithm below directly provides optimal pruning.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
Constraint-Pruned Backtracking with Single-Loop 3-Way Validation.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
private:
    bool isValid(const vector<vector<char>>& board, int row, int col, char c) {
        for (int i = 0; i < 9; i++) {
            // Check row
            if (board[row][i] == c) return false;
            // Check column
            if (board[i][col] == c) return false;
            // Check 3x3 sub-box
            if (board[3 * (row / 3) + i / 3][3 * (col / 3) + i % 3] == c) return false;
        }
        return true;
    }

public:
    bool solveSudoku(vector<vector<char>>& board) {
        for (int i = 0; i < 9; i++) {
            for (int j = 0; j < 9; j++) {
                if (board[i][j] == '.') {
                    for (char c = '1'; c <= '9'; c++) {
                        if (isValid(board, i, j, c)) {
                            board[i][j] = c;
                            
                            if (solveSudoku(board)) {
                                return true; // solution found
                            }
                            
                            board[i][j] = '.'; // backtrack
                        }
                    }
                    return false; // no digit from 1-9 was valid
                }
            }
        }
        return true; // all cells filled
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(9^M)$ where $M \le 81$ is the number of empty cells (runs in $< 10\text{ms}$ for valid puzzles).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space (maximum recursion depth 81 on fixed $9 \times 9$ grid).
- **Why this is optimal**: Compact 3-in-1 loop validation and immediate boolean propagation stops search upon finding valid solution.

---

## 6. Dry Run

Sudoku Grid Resolution

| Step | Action / State Change | Result |
|---|---|---|
| `Empty Cell (0,2)` | Check digits 1-9: '4' is valid | board[0][2] = '4' |
| `Next Empty (0,5)` | Check digits 1-9: '7' is valid | board[0][5] = '7' |
| `Full board filled` | All 81 cells valid | Return TRUE ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Already solved puzzle (returns `true` immediately).
- Hard puzzle with multiple dead-ends.

### Common Bugs to Avoid
- Forgetting `return false` after testing digits 1-9 (allows continuing with invalid unfilled cells).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How does 3 * (row / 3) + i / 3 locate the 3x3 box cells?**  
  **A**: Integer division `(row / 3) * 3` gives top-left box row (0, 3, or 6). Adding `i / 3` steps through the 3 rows, while `i % 3` steps through the 3 columns.


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Backtracking`, `Sudoku`, `LeetCode-37`, `Hard`
- **Related problems to practice next**:
- **N-Queens**: Constraint placement.
