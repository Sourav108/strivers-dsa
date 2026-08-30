# Word Search (Exist on Grid) (Step 7.3 — Hard Recursion Problems & Backtracking)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Word Search (Exist on Grid)](https://takeuforward.org/data-structure/word-search-leetcode/)
- **Difficulty**: Medium
- **Statement**: Given an $m \times n$ grid of characters `board` and a string `word`, return `true` if `word` exists in the grid. The word can be constructed from letters of sequentially adjacent cells (horizontally or vertically neighboring). The same letter cell may not be used more than once in a word.

---

## 1. Problem, Restated

Perform 4-directional DFS backtracking with in-place cell visited masking.

- **Input**: Grid / String / Constraints.
- **Output**: Boolean feasibility or complete vector of solution configurations.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

For each cell $(r, c)$ where `board[r][c] == word[0]`: run DFS `search(r, c, 0)`. In DFS: 1) If `idx == word.length()`, return `true`. 2) If out of bounds or `board[r][c] != word[idx]`, return `false`. 3) Mask `char temp = board[r][c]; board[r][c] = '#';` (marks visited in $\mathcal{O}(1)$ space). 4) Explore 4 neighbors (Up, Down, Left, Right). 5) Unmask `board[r][c] = temp;` (backtrack).

- **Underlying Pattern**: `2D Grid DFS Backtracking with In-Place Masking (`board[r][c] = '#'` and restore)`.

---

## 3. Approach 1 — Naive / Unpruned Search

### Idea
Maintaining an auxiliary $M \times N$ visited boolean matrix on every search.

### C++17 Code
```cpp
// Visited matrix approach
```

### Java Code
```java
// Java equivalent
// Visited matrix approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(M \cdot N \cdot 4^L)$.
- **Space Complexity**: $\mathcal{O}(M \cdot N)$ space.
- **Why it's not good enough**: Allocates extra matrix.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard backtracking algorithm below directly provides optimal pruning.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
In-Place Grid Character Masking in $\mathcal{O}(L)$ auxiliary space.

### C++17 Code
```cpp
#include <vector>
#include <string>
using namespace std;

class Solution {
private:
    bool dfs(int r, int c, int index, vector<vector<char>>& board, const string& word) {
        if (index == (int)word.length()) return true;
        
        int m = board.size();
        int n = board[0].size();
        
        // Boundary and mismatch check
        if (r < 0 || r >= m || c < 0 || c >= n || board[r][c] != word[index]) {
            return false;
        }
        
        // In-place mark visited
        char temp = board[r][c];
        board[r][c] = '#';
        
        // Explore 4 directions
        bool found = dfs(r + 1, c, index + 1, board, word) ||
                     dfs(r - 1, c, index + 1, board, word) ||
                     dfs(r, c + 1, index + 1, board, word) ||
                     dfs(r, c - 1, index + 1, board, word);
        
        // Backtrack (restore original character)
        board[r][c] = temp;
        
        return found;
    }

public:
    bool exist(vector<vector<char>>& board, string word) {
        int m = board.size();
        int n = board[0].size();
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (board[i][j] == word[0] && dfs(i, j, 0, board, word)) {
                    return true;
                }
            }
        }
        
        return false;
    }
};
```

### Java Code
```java
class Solution {

    boolean dfs(int r, int c, int index, char[][] board, String word) {
        if (index == word.length()) return true;
        
        int m = board.length;
        int n = board[0].size();
        
        // Boundary and mismatch check
        if (r < 0 || r >= m || c < 0 || c >= n || board[r][c] != word[index]) {
            return false;
        }
        
        // In-place mark visited
        char temp = board[r][c];
        board[r][c] = '#';
        
        // Explore 4 directions
        boolean found = dfs(r + 1, c, index + 1, board, word) ||
                     dfs(r - 1, c, index + 1, board, word) ||
                     dfs(r, c + 1, index + 1, board, word) ||
                     dfs(r, c - 1, index + 1, board, word);
        
        // Backtrack (restore original character)
        board[r][c] = temp;
        
        return found;
    }

    boolean exist(char[][] board, String word) {
        int m = board.length;
        int n = board[0].size();
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (board[i][j] == word[0] && dfs(i, j, 0, board, word)) {
                    return true;
                }
            }
        }
        
        return false;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(M \cdot N \cdot 3^L)$ where $L$ is word length (3 branching choices after the first step).
- **Space Complexity**: $\mathcal{O}(L)$ recursion call stack space.
- **Why this is optimal**: Zero auxiliary matrix memory through in-place character masking.

---

## 6. Dry Run

`board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]]`, `word = "ABCCED"`

| Step | Action / State Change | Result |
|---|---|---|
| `Start (0,0) 'A'` | A -> B(0,1) -> C(0,2) -> C(1,2) -> E(2,2) -> D(2,1) | Matched all 6 chars |
| `Result` | Word fully found | Return TRUE ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Word longer than total grid cells ($L > M \times N \implies$ false).
- Single cell grid matching/mismatching.

### Common Bugs to Avoid
- Forgetting to unmask `board[r][c] = temp` during backtrack return.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is branching factor 3^L instead of 4^L?**  
  **A**: Because we never move backward into the cell we just came from (since it is masked as `'#'`), leaving at most 3 valid direction choices per step.


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Backtracking`, `Grid DFS`, `LeetCode-79`, `Medium`
- **Related problems to practice next**:
- **Rat in a Maze**: Grid pathfinding.
- **Word Search II**: Trie + Grid DFS.
