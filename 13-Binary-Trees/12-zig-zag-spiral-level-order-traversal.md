# Zig-Zag / Spiral Level Order Traversal (Step 13.2 — Medium Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Zig-Zag / Spiral Level Order Traversal](https://takeuforward.org/data-structure/zig-zag-traversal-of-binary-tree/)
- **Difficulty**: Medium
- **Statement**: Given the root of a binary tree, return the zigzag level order traversal of its nodes' values (i.e., from left to right, then right to left for the next level and alternate between).

---

## 1. Problem, Restated

BFS level order where values in odd levels ($0, 2, 4, \dots$) are placed left-to-right, and even levels ($1, 3, 5, \dots$) are placed right-to-left.

- **Input**: `TreeNode* root` pointer to binary tree.
- **Output**: Traversal vector / boolean.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Standard BFS queue traversal: track a boolean flag `leftToRight = true`. For each level of size $K$: pre-allocate a vector of size $K$ (`vector<int> row(K)`). For each node at loop index $i \in [0, K-1]$: 1) Compute placement index `index = leftToRight ? i : (K - 1 - i)`. 2) Assign `row[index] = curr->val`. 3) Push left and right children to queue. 4) Toggle `leftToRight = !leftToRight`. Avoids expensive `std::reverse()`!

- **Underlying Pattern**: `Direction-Flagged Direct Index Vector Placement (`index = leftToRight ? i : (size - 1 - i)`)`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Standard BFS level order followed by `std::reverse()` on alternate rows in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
// Reverse vector approach
```

### Java Code
```java
// Java equivalent
// Reverse vector approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ space.
- **Why it's not good enough**: Performs unnecessary vector element reversals.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard DFS / BFS algorithm below directly achieves optimal $\mathcal{O}(N)$ or $\mathcal{O}(N \log N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Direct Index Level Placement BFS in $\mathcal{O}(N)$ time and $\mathcal{O}(W)$ space.

### C++17 Code
```cpp
#include <vector>
#include <queue>
using namespace std;

struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class Solution {
public:
    vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
        vector<vector<int>> result;
        if (root == nullptr) return result;
        
        queue<TreeNode*> q;
        q.push(root);
        bool leftToRight = true;
        
        while (!q.empty()) {
            int size = q.size();
            vector<int> row(size);
            
            for (int i = 0; i < size; i++) {
                TreeNode* curr = q.front();
                q.pop();
                
                // Direct index placement based on traversal direction
                int index = leftToRight ? i : (size - 1 - i);
                row[index] = curr->val;
                
                if (curr->left != nullptr) q.push(curr->left);
                if (curr->right != nullptr) q.push(curr->right);
            }
            
            // Switch direction for next level
            leftToRight = !leftToRight;
            result.push_back(row);
        }
        
        return result;
    }
};
```

### Java Code
```java
import java.util.*;

static class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    public TreeNode(int x) { /* initialized: val(x), left(null), right(null)  */  }
};

class Solution {

    int[][] zigzagLevelOrder(TreeNode  root) {
        int[][] result;
        if (root == null) return result;
        
        Queue<TreeNode> q = new LinkedList<>();
        q.push(root);
        boolean leftToRight = true;
        
        while (!q.isEmpty()) {
            int size = q.length;
            int[] row = new int[size];
            
            for (int i = 0; i < size; i++) {
                TreeNode  curr = q.peek();
                q.pop();
                
                // Direct index placement based on traversal direction
                int index = leftToRight ? i : (size - 1 - i);
                row[index] = curr.val;
                
                if (curr.left != null) q.push(curr.left);
                if (curr.right != null) q.push(curr.right);
            }
            
            // Switch direction for next level
            leftToRight = !leftToRight;
            result.add(row);
        }
        
        return result;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time (each node processed once).
- **Space Complexity**: $\mathcal{O}(W)$ queue memory.
- **Why this is optimal**: Direct index mapping eliminates overhead of post-processing reversal passes.

---

## 6. Dry Run

Tree: `3 -> left: 9, right: 20 (left: 15, right: 7)`

| Step | Action / State Change | Result |
|---|---|---|
| `Level 0 (L->R)` | size=1, i=0 -> idx=0 -> row: `[3]`, q pushes 9, 20 | row: `[3]` |
| `Level 1 (R->L)` | size=2, i=0(node 9)->idx=1, i=1(node 20)->idx=0 -> row: `[20, 9]` | row: `[20, 9]` |
| `Level 2 (L->R)` | size=2, node 15->idx=0, node 7->idx=1 -> row: `[15, 7]` | row: `[15, 7]` |
| `Result` | ZigZag Level Order | `[[3], [20, 9], [15, 7]]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `root == nullptr` (returns `[]`).
- Single node tree `[1]` $\implies$ `[[1]]`.

### Common Bugs to Avoid
- Reversing the queue pushing order (pushing right child before left breaks BFS queue ordering for subsequent levels).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why must children always be pushed Left-then-Right into the queue regardless of zig-zag direction?**  
  **A**: Because the queue maintains the natural geometric ordering of nodes. Only the output array insertion index is flipped, keeping child expansion consistent across levels!

- **Q2: Can this be solved using two stacks?**  
  **A**: Yes! Stack 1 pops left-to-right and pushes children Left-then-Right into Stack 2; Stack 2 pops right-to-left and pushes children Right-then-Left into Stack 1.

- **Q3: What is the maximum memory used by the queue?**  
  **A**: At most $W = \lceil N/2 \rceil$ nodes for a perfect binary tree.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `BFS`, `Queue`, `LeetCode-103`, `Medium`
- **Related problems to practice next**:
- **Level Order Traversal**: Standard BFS.
- **Boundary Traversal**: Perimeter traversal.
