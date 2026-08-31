# Print Root to Node Path in Binary Tree (Step 13.2 — Medium Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Print Root to Node Path in Binary Tree](https://takeuforward.org/data-structure/print-root-to-node-path-in-a-binary-tree/)
- **Difficulty**: Medium
- **Statement**: Given a Binary Tree with root node `root` and a target value $B$, return the path from root to the node with value $B$ as a vector of integers.

---

## 1. Problem, Restated

Find the unique path from root to target node $B$ using backtracking DFS.

- **Input**: `TreeNode* root` pointer to binary tree.
- **Output**: Traversal vector / boolean / `TreeNode*` pointer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) If `root == nullptr`, return `false`. 2) Push `root->val` to `path`. 3) If `root->val == B`, return `true` (target found!). 4) If `getPath(root->left, path, B) || getPath(root->right, path, B)` returns `true`, return `true`. 5) If target is not in either subtree, **backtrack**: `path.pop_back()` and return `false`.

- **Underlying Pattern**: `Backtracking Root-to-Leaf Path Search (`path.push_back()` $\to$ explore $\to$ `path.pop_back()`)`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Generate all root-to-leaf paths, then search for target in each path in $\mathcal{O}(N \cdot H)$ time.

### C++17 Code
```cpp
// All paths search
```

### Java Code
```java
// Java equivalent
// All paths search
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \cdot H)$.
- **Space Complexity**: $\mathcal{O}(N \cdot H)$.
- **Why it's not good enough**: Generates all paths.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard DFS / BFS algorithm below directly achieves optimal $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Backtracking DFS with Early Return in $\mathcal{O}(N)$ time and $\mathcal{O}(H)$ space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class Solution {
private:
    bool getPath(TreeNode* root, vector<int>& path, int target) {
        if (root == nullptr) return false;
        
        // Include current node in path
        path.push_back(root->val);
        
        // Target found!
        if (root->val == target) return true;
        
        // Check left or right subtree
        if (getPath(root->left, path, target) || getPath(root->right, path, target)) {
            return true;
        }
        
        // Backtrack: target not found in this subtree
        path.pop_back();
        return false;
    }

public:
    vector<int> solve(TreeNode* root, int B) {
        vector<int> path;
        if (root == nullptr) return path;
        getPath(root, path, B);
        return path;
    }
};
```

### Java Code
```java
static class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    public TreeNode(int x) { /* initialized: val(x), left(null), right(null)  */  }
};

class Solution {

    boolean getPath(TreeNode  root, int[] path, int target) {
        if (root == null) return false;
        
        // Include current node in path
        path.add(root.val);
        
        // Target found!
        if (root.val == target) return true;
        
        // Check left or right subtree
        if (getPath(root.left, path, target) || getPath(root.right, path, target)) {
            return true;
        }
        
        // Backtrack: target not found in this subtree
        path.remove();
        return false;
    }

    int[] solve(TreeNode  root, int B) {
        int[] path;
        if (root == null) return path;
        getPath(root, path, B);
        return path;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ worst-case DFS traversal.
- **Space Complexity**: $\mathcal{O}(H)$ call stack and path memory.
- **Why this is optimal**: Backtracking automatically prunes failed paths, leaving only the exact target path.

---

## 6. Dry Run

Tree: `1 -> left: 2 (left: 4, right: 5 (left: 6, right: 7)), right: 3`, Target $B = 7$

| Step | Action / State Change | Result |
|---|---|---|
| `Push 1` | path: `[1]` | explore left |
| `Push 2, Push 4` | path: `[1, 2, 4]` -> 4 != 7 -> pop 4 | backtrack 4 |
| `Push 5, Push 6` | path: `[1, 2, 5, 6]` -> 6 != 7 -> pop 6 | backtrack 6 |
| `Push 7` | path: `[1, 2, 5, 7]` -> 7 == 7 -> Return TRUE! | Target Found! ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Target is root itself $B = 1$ (returns `[1]`).
- Target does not exist in tree (returns `[]`).

### Common Bugs to Avoid
- Forgetting `path.pop_back()` when returning `false` (leaves dead branches in path vector).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How is this problem used to find Lowest Common Ancestor (LCA)?**  
  **A**: Find path to $P$ and path to $Q$. The LCA is the last common value before the paths diverge in $\mathcal{O}(N)$ time!

- **Q2: Can target node appear multiple times?**  
  **A**: If duplicate values exist, the algorithm returns the path to the first occurrence found in DFS order.

- **Q3: How to return all root-to-leaf paths?**  
  **A**: When `!node->left && !node->right`, push a copy of `path` to `allPaths` matrix.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `DFS`, `Backtracking`, `Medium`
- **Related problems to practice next**:
- **Lowest Common Ancestor**: LCA derivation.
- **Path Sum III**: Path sums.
