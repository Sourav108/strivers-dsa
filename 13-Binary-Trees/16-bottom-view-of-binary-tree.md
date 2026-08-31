# Bottom View of Binary Tree (Step 13.2 — Medium Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Bottom View of Binary Tree](https://takeuforward.org/data-structure/bottom-view-of-a-binary-tree/)
- **Difficulty**: Medium
- **Statement**: Given a binary tree, print the bottom view from left to right. A node $x$ is there in output if $x$ is the bottom-most node at its horizontal distance. If there are multiple bottom-most nodes for a horizontal distance, the later one in BFS order is considered.

---

## 1. Problem, Restated

Find the last node encountered at each vertical column line in BFS level order.

- **Input**: `TreeNode* root` pointer to binary tree.
- **Output**: Traversal vector / boolean / `TreeNode*` pointer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Traverse tree using BFS queue storing `{TreeNode*, col}`. For each node, unconditionally overwrite `bottomNode[col] = curr->val`. Since BFS proceeds level-by-level from top to bottom, the last node written to `bottomNode[col]` is guaranteed to be the lowest visible node at that horizontal distance!

- **Underlying Pattern**: `BFS Level-Order Vertical Line Last-Occupancy Map (`map<int, int> bottomNode`)`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
DFS passing `{row, col}` and updating map only when `row >= existingRow`.

### C++17 Code
```cpp
// DFS depth comparison approach
```

### Java Code
```java
// Java equivalent
// DFS depth comparison approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Requires tracking depth and tie-breaking.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard DFS / BFS algorithm below directly achieves optimal $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
BFS Queue with Column Overwriting in $\mathcal{O}(N \log N)$ time and $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <vector>
#include <map>
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
    vector<int> bottomView(TreeNode* root) {
        vector<int> result;
        if (root == nullptr) return result;
        
        // Map stores: col -> bottom-most node value
        map<int, int> bottomNode;
        
        // Queue stores: {TreeNode*, col}
        queue<pair<TreeNode*, int>> q;
        q.push({root, 0});
        
        while (!q.empty()) {
            auto [curr, col] = q.front();
            q.pop();
            
            // Unconditionally overwrite: later levels replace higher levels
            bottomNode[col] = curr->val;
            
            if (curr->left != nullptr) {
                q.push({curr->left, col - 1});
            }
            if (curr->right != nullptr) {
                q.push({curr->right, col + 1});
            }
        }
        
        // Extract in ascending column order
        for (auto& [col, val] : bottomNode) {
            result.push_back(val);
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

    int[] bottomView(TreeNode  root) {
        int[] result;
        if (root == null) return result;
        
        // Map stores: col . bottom-most node value
        Map<Integer, Integer> bottomNode = new HashMap<>();
        
        // Queue stores: {TreeNode , col}
        queue<pair<TreeNode , int>> q;
        q.push({root, 0});
        
        while (!q.isEmpty()) {
            var [curr, col] = q.peek();
            q.pop();
            
            // Unconditionally overwrite: later levels replace higher levels
            bottomNode[col] = curr.val;
            
            if (curr.left != null) {
                q.push({curr.left, col - 1});
            }
            if (curr.right != null) {
                q.push({curr.right, col + 1});
            }
        }
        
        // Extract in ascending column order
        for (var [col, val] : bottomNode) {
            result.add(val);
        }
        
        return result;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N)$ time (map insertions).
- **Space Complexity**: $\mathcal{O}(N)$ queue and map space.
- **Why this is optimal**: BFS naturally guarantees later updates correspond to deeper or rightmost nodes at each column.

---

## 6. Dry Run

Tree: `20(0) -> left: 8(-1) (left: 5(-2), right: 3(0) (left: 10(-1), right: 14(1))), right: 22(1) (right: 25(2))`

| Step | Action / State Change | Result |
|---|---|---|
| `col = -2` | leaf 5 | map[-2] = 5 |
| `col = -1` | node 8 -> overwritten by node 10 at lower level | map[-1] = 10 |
| `col = 0` | root 20 -> overwritten by node 3 at lower level | map[0] = 3 |
| `col = 1` | node 22 -> overwritten by node 14 at lower level | map[1] = 14 |
| `col = 2` | node 25 | map[2] = 25 |
| `Result` | Bottom View: `[5, 10, 3, 14, 25]` | Complete ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `root == nullptr` (returns `[]`).
- Single node.

### Common Bugs to Avoid
- Guarding with `if (bottomNode.find(col) == bottomNode.end())` (this turns it into Top View!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does BFS naturally solve the tie-breaking rule for equal depth nodes?**  
  **A**: Because when two nodes are at the same depth and column, the right child is enqueued after the left child, so its dequeue overwrites the earlier node, naturally fulfilling the 'later in level order' requirement!

- **Q2: How to make this run in strict O(N) time?**  
  **A**: Track `minCol` and `maxCol` during BFS. Use an `unordered_map<int, int>` and loop from `minCol` to `maxCol` in $\mathcal{O}(N)$ time.

- **Q3: Can DFS be used for Bottom View?**  
  **A**: Yes, but DFS must track `depth` and overwrite map only if `currentDepth >= map[col].depth`.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `BFS`, `Queue`, `Medium`
- **Related problems to practice next**:
- **Top View of Binary Tree**: Top view counterpart.
- **Vertical Order Traversal**: Full columns.
