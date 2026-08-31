# Top View of Binary Tree (Step 13.2 — Medium Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Top View of Binary Tree](https://takeuforward.org/data-structure/top-view-of-a-binary-tree/)
- **Difficulty**: Medium
- **Statement**: Given below is a binary tree. The task is to print the top view of binary tree. Top view of a binary tree is the set of nodes visible when the tree is viewed from the top.

---

## 1. Problem, Restated

First node encountered at each vertical line column $x$ in BFS level order.

- **Input**: `TreeNode* root` pointer to binary tree.
- **Output**: Traversal vector / boolean.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

BFS visits nodes in increasing order of vertical depth (level by level). For each node at column `col`: if `col` is NOT yet in `map<int, int> topNode`, insert `topNode[col] = node->val`! Since BFS processes top levels first, the first node inserted for column `col` is guaranteed to be the topmost visible node. Extract map values from leftmost column to rightmost column.

- **Underlying Pattern**: `BFS Level-Order Vertical Line First-Occupancy Map (`map<int, int> topNode`)`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
DFS traversal tracking `{node->val, depth}` and overwriting on smaller depth.

### C++17 Code
```cpp
// DFS top view approach
```

### Java Code
```java
// Java equivalent
// DFS top view approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Requires tracking depth.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard DFS / BFS algorithm below directly achieves optimal $\mathcal{O}(N)$ or $\mathcal{O}(N \log N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
BFS Queue with Column Hash Map in $\mathcal{O}(N \log N)$ time and $\mathcal{O}(N)$ space.

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
    vector<int> topView(TreeNode* root) {
        vector<int> result;
        if (root == nullptr) return result;
        
        // Map stores: col -> topmost node value
        map<int, int> topNode;
        
        // Queue stores: {TreeNode*, col}
        queue<pair<TreeNode*, int>> q;
        q.push({root, 0});
        
        while (!q.empty()) {
            auto [curr, col] = q.front();
            q.pop();
            
            // Only store the first node seen at this vertical column
            if (topNode.find(col) == topNode.end()) {
                topNode[col] = curr->val;
            }
            
            if (curr->left != nullptr) {
                q.push({curr->left, col - 1});
            }
            if (curr->right != nullptr) {
                q.push({curr->right, col + 1});
            }
        }
        
        // Collect sorted column results from left to right
        for (auto& [col, val] : topNode) {
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

    int[] topView(TreeNode  root) {
        int[] result;
        if (root == null) return result;
        
        // Map stores: col . topmost node value
        Map<Integer, Integer> topNode = new HashMap<>();
        
        // Queue stores: {TreeNode , col}
        queue<pair<TreeNode , int>> q;
        q.push({root, 0});
        
        while (!q.isEmpty()) {
            var [curr, col] = q.peek();
            q.pop();
            
            // Only store the first node seen at this vertical column
            if (topNode.find(col) == topNode.end()) {
                topNode[col] = curr.val;
            }
            
            if (curr.left != null) {
                q.push({curr.left, col - 1});
            }
            if (curr.right != null) {
                q.push({curr.right, col + 1});
            }
        }
        
        // Collect sorted column results from left to right
        for (var [col, val] : topNode) {
            result.add(val);
        }
        
        return result;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N)$ time (map insertion per node).
- **Space Complexity**: $\mathcal{O}(N)$ queue and map space.
- **Why this is optimal**: BFS guarantees that the first node processed for each column line is the topmost visible node.

---

## 6. Dry Run

Tree: `1(0) -> left: 2(-1) (right: 4(0) -> right: 5(1) -> right: 6(2))), right: 3(1)`

| Step | Action / State Change | Result |
|---|---|---|
| `Root 1 (col 0)` | topNode[0] = 1 | map: `{0: 1}` |
| `Node 2 (col -1)` | topNode[-1] = 2 | map: `{-1: 2, 0: 1}` |
| `Node 3 (col 1)` | topNode[1] = 3 | map: `{-1: 2, 0: 1, 1: 3}` |
| `Node 4 (col 0)` | col 0 already in map -> ignore | map unchanged |
| `Node 6 (col 2)` | topNode[2] = 6 | map: `{-1: 2, 0: 1, 1: 3, 2: 6}` |
| `Result` | Left to right columns `[-1, 0, 1, 2]` | Top View: `[2, 1, 3, 6]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `root == nullptr` (returns `[]`).
- Skewed trees.

### Common Bugs to Avoid
- Overwriting map entries `topNode[col] = curr->val` (overwriting turns Top View into Bottom View!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does DFS sometimes produce incorrect Top View if not tracking depth?**  
  **A**: Because DFS can go deep into the right subtree and visit a lower node at column $0$ BEFORE visiting a higher node at column $0$ in the left subtree. BFS guarantees level-by-level top-down order!

- **Q2: How to optimize map lookup from O(N log N) to O(N)?**  
  **A**: Track `minCol` and `maxCol` integers during BFS. Use an `unordered_map<int, int>` and loop `col` from `minCol` to `maxCol` in strict $\mathcal{O}(N)$ time!

- **Q3: What is the difference between Top View and Bottom View?**  
  **A**: In Top View, we insert ONLY IF column is not in map (`if (topNode.find(col) == topNode.end())`). In Bottom View, we OVERWRITE unconditionally on every visit (`bottomNode[col] = curr->val`).


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `BFS`, `Queue`, `Medium`
- **Related problems to practice next**:
- **Bottom View of Binary Tree**: Bottom view counterpart.
- **Vertical Order Traversal**: Full vertical columns.
