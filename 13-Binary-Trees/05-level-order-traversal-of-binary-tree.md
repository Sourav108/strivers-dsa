# Level Order Traversal / BFS of Binary Tree (Step 13.1 — Traversals)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Level Order Traversal / BFS of Binary Tree](https://takeuforward.org/data-structure/level-order-traversal-of-a-binary-tree/)
- **Difficulty**: Easy
- **Statement**: Given the root of a binary tree, return the level order traversal of its nodes' values (i.e., from left to right, level by level) as a 2D vector `vector<vector<int>>`.

---

## 1. Problem, Restated

Breadth-First Search (BFS) level-by-level traversal using a FIFO Queue.

- **Input**: `TreeNode* root` pointer to the root of a binary tree.
- **Output**: Traversal vector or tree node construction.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Push `root` into a FIFO queue `queue<TreeNode*> q`. While queue is not empty: 1) Record `levelSize = q.size()`. 2) Loop `levelSize` times: pop `curr = q.front()`, add `curr->val` to `currentLevel` vector, push `curr->left` if non-null, push `curr->right` if non-null. 3) Push `currentLevel` into `result` vector. Runs in strict $\mathcal{O}(N)$ time.

- **Underlying Pattern**: `FIFO Queue Level-Sized BFS Iteration (`int sz = q.size()`)`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Compute height $H$, then call recursive `printLevel(root, d)` for $d = 1$ to $H$ in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
// O(N^2) recursive level scans
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(H)$.
- **Why it's not good enough**: Repeatedly traverses top levels.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard iterative and recursive algorithms below directly achieve optimal bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Iterative / Production)

### Idea
Standard FIFO Queue Level-by-Level BFS in $\mathcal{O}(N)$ time and $\mathcal{O}(W)$ space.

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
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> result;
        if (root == nullptr) return result;
        
        queue<TreeNode*> q;
        q.push(root);
        
        while (!q.empty()) {
            int levelSize = q.size(); // number of nodes at current depth level
            vector<int> currentLevel;
            currentLevel.reserve(levelSize);
            
            for (int i = 0; i < levelSize; i++) {
                TreeNode* curr = q.front();
                q.pop();
                
                currentLevel.push_back(curr->val);
                
                if (curr->left != nullptr) {
                    q.push(curr->left);
                }
                if (curr->right != nullptr) {
                    q.push(curr->right);
                }
            }
            
            result.push_back(currentLevel);
        }
        
        return result;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time (each node is enqueued and dequeued exactly once).
- **Space Complexity**: $\mathcal{O}(W)$ queue memory where $W$ is the maximum width of the tree ($W \le N/2$ for perfect binary tree).
- **Why this is optimal**: Queue snapshot `levelSize = q.size()` naturally partitions nodes into discrete levels without sentinel nulls.

---

## 6. Dry Run

Tree: `3 -> left: 9, right: 20 (left: 15, right: 7)`

| Step | Action / State Change | Result |
|---|---|---|
| `Level 0` | q: `[3]`, levelSize = 1 -> pop 3, push 9, 20 | level 0: `[3]` |
| `Level 1` | q: `[9, 20]`, levelSize = 2 -> pop 9 (no children), pop 20 (push 15, 7) | level 1: `[9, 20]` |
| `Level 2` | q: `[15, 7]`, levelSize = 2 -> pop 15, pop 7 | level 2: `[15, 7]` |
| `Result` | 2D Level Order | `[[3], [9, 20], [15, 7]]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `root == nullptr` (returns `[]`).
- Single node `[1]` $\implies$ `[[1]]`.

### Common Bugs to Avoid
- Using `i < q.size()` directly in loop condition without snapshotting `int levelSize = q.size()` (causes dynamic size corruption as children are pushed).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is freezing levelSize = q.size() before the loop mandatory?**  
  **A**: Because pushing child nodes into the queue increases `q.size()` dynamically during the loop. Freezing `levelSize` ensures the loop processes ONLY the nodes belonging to the current depth level!

- **Q2: What is the maximum space complexity of BFS on a binary tree?**  
  **A**: For a Perfect Binary Tree of $N$ nodes, the last leaf level contains $\lceil N/2 \rceil$ nodes. Thus maximum queue space is $\mathcal{O}(N/2) = \mathcal{O}(N)$.

- **Q3: How does Level Order Traversal differ from DFS in shortest-path finding?**  
  **A**: BFS explores nodes in increasing order of distance (edges) from root, guaranteeing that the first time a target node is visited, it is reached via the shortest path.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `BFS`, `Queue`, `LeetCode-102`, `Easy`
- **Related problems to practice next**:
- **Zig-Zag Level Order**: Alternating BFS.
- **Maximum Width of Binary Tree**: Indexed BFS.
