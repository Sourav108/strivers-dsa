# N-Queens Problem (Branch & Bound bit masking) (Step 7.3 — Hard Recursion Problems & Backtracking)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [N-Queens Problem (Branch & Bound bit masking)](https://takeuforward.org/data-structure/n-queen-problem-return-all-distinct-solutions-to-the-n-queens-puzzle/)
- **Difficulty**: Hard
- **Statement**: The $n$-queens puzzle is the problem of placing $n$ queens on an $n \times n$ chessboard such that no two queens attack each other. Given an integer $n$, return all distinct solutions to the $n$-queens puzzle.

---

## 1. Problem, Restated

Place $N$ queens column by column with $\mathcal{O}(1)$ safety checks using 3 hash vectors.

- **Input**: Grid / String / Constraints.
- **Output**: Boolean feasibility or complete vector of solution configurations.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Queens attack along: 1) Same Row, 2) Lower-Diagonal ($\text{row} + \text{col}$ is constant), 3) Upper-Diagonal ($N - 1 + \text{col} - \text{row}$ is constant). Using three boolean hash arrays `leftRow[N]`, `lowerDiag[2N-1]`, and `upperDiag[2N-1]`, we check if a square is safe in strict $\mathcal{O}(1)$ time without scanning lines!

- **Underlying Pattern**: `Column-by-Column Backtracking + 3 Directional Hash Arrays`.

---

## 3. Approach 1 — Naive / Unpruned Search

### Idea
Looping in 3 directions ($\leftarrow, 
warrow, \swarrow$) taking $\mathcal{O}(N)$ per queen placement check.

### C++17 Code
```cpp
// O(N) line scan isSafe function
```

### Java Code
```java
// Java equivalent
// O(N) line scan isSafe function
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N! \cdot N)$ time.
- **Space Complexity**: $\mathcal{O}(N^2)$.
- **Why it's not good enough**: Redundant directional linear scans.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard backtracking algorithm below directly provides optimal pruning.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
Branch & Bound with 3 $\mathcal{O}(1)$ Direct Lookup Hash Arrays.

### C++17 Code
```cpp
#include <vector>
#include <string>
using namespace std;

class Solution {
private:
    void solve(int col, vector<string>& board, vector<vector<string>>& ans,
               vector<int>& leftRow, vector<int>& lowerDiag, vector<int>& upperDiag, int n) {
        if (col == n) {
            ans.push_back(board);
            return;
        }
        
        for (int row = 0; row < n; row++) {
            // O(1) safety check using 3 direction hashes
            if (leftRow[row] == 0 && lowerDiag[row + col] == 0 && upperDiag[n - 1 + col - row] == 0) {
                // Place Queen
                board[row][col] = 'Q';
                leftRow[row] = 1;
                lowerDiag[row + col] = 1;
                upperDiag[n - 1 + col - row] = 1;
                
                solve(col + 1, board, ans, leftRow, lowerDiag, upperDiag, n);
                
                // Backtrack
                board[row][col] = '.';
                leftRow[row] = 0;
                lowerDiag[row + col] = 0;
                upperDiag[n - 1 + col - row] = 0;
            }
        }
    }

public:
    vector<vector<string>> solveNQueens(int n) {
        vector<vector<string>> ans;
        vector<string> board(n, string(n, '.'));
        
        vector<int> leftRow(n, 0);
        vector<int> lowerDiag(2 * n - 1, 0);
        vector<int> upperDiag(2 * n - 1, 0);
        
        solve(0, board, ans, leftRow, lowerDiag, upperDiag, n);
        return ans;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    void solve(int col, String[] board, List<List<String>> ans,
               int[] leftRow, int[] lowerDiag, int[] upperDiag, int n) {
        if (col == n) {
            ans.add(board);
            return;
        }
        
        for (int row = 0; row < n; row++) {
            // O(1) safety check using 3 direction hashes
            if (leftRow[row] == 0 && lowerDiag[row + col] == 0 && upperDiag[n - 1 + col - row] == 0) {
                // Place Queen
                board[row][col] = 'Q';
                leftRow[row] = 1;
                lowerDiag[row + col] = 1;
                upperDiag[n - 1 + col - row] = 1;
                
                solve(col + 1, board, ans, leftRow, lowerDiag, upperDiag, n);
                
                // Backtrack
                board[row][col] = '.';
                leftRow[row] = 0;
                lowerDiag[row + col] = 0;
                upperDiag[n - 1 + col - row] = 0;
            }
        }
    }

    List<List<String>> solveNQueens(int n) {
        List<List<String>> ans;
        String[] board(n, String(n, '.'));
        
        int[] leftRow = new int[n];
        int[] lowerDiag = new int[2 * n - 1];
        int[] upperDiag = new int[2 * n - 1];
        
        solve(0, board, ans, leftRow, lowerDiag, upperDiag, n);
        return ans;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N!)$ time (first queen has $N$ choices, second has $N-2$, etc.).
- **Space Complexity**: $\mathcal{O}(N)$ auxiliary space for lookup vectors + board.
- **Why this is optimal**: Instantaneous $\mathcal{O}(1)$ safety check eliminates geometric line scanning.

---

## 6. Dry Run

$N = 4$ (Solutions: 2)

| Step | Action / State Change | Result |
|---|---|---|
| `Col 0` | Row 1: place 'Q' | board[1][0] = 'Q' |
| `Col 1` | Row 3: place 'Q' | board[3][1] = 'Q' |
| `Col 2` | Row 0: place 'Q' | board[0][2] = 'Q' |
| `Col 3` | Row 2: place 'Q' | board[2][3] = 'Q' |
| `Solution 1` | Found `[".Q..","...Q","Q...","..Q."]` | Valid Board 1 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 1$ (`[["Q"]]`).
- $N = 2, 3$ (no solutions exist $\implies$ `[]`).

### Common Bugs to Avoid
- Indexing `upperDiag` without offset `n - 1` (causes negative array index `col - row < 0`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is upper diagonal indexed as (n - 1 + col - row)?**  
  **A**: Because $(col - row)$ ranges from $-(n - 1)$ to $(n - 1)$. Adding $(n - 1)$ shifts all indices into the non-negative range $[0, 2n - 2]$.


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Backtracking`, `N-Queens`, `LeetCode-51`, `Hard`
- **Related problems to practice next**:
- **Sudoku Solver**: Exact cover backtracking.
