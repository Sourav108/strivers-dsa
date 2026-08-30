# Maximum Width of Binary Tree (Indexed BFS) (Step 13.2 — Medium Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Maximum Width of Binary Tree (Indexed BFS)](https://takeuforward.org/data-structure/maximum-width-of-a-binary-tree/)
- **Difficulty**: Medium
- **Statement**: Given the root of a binary tree, return the maximum width of the given tree. The maximum width of a tree is the maximum width among all levels. The width of one level is defined as the length between the end-nodes (the leftmost and rightmost non-null nodes), where the null nodes between the end-nodes that would be present in a complete binary tree extending down to that level are also counted into the length calculation.

---

## 1. Problem, Restated

Find the maximum $(rightIndex - leftIndex + 1)$ across all levels using 0-based normalized index BFS.

- **Input**: `TreeNode* root` pointer to binary tree.
- **Output**: Integer width / count / list of nodes / boolean.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

In a Complete Binary Tree, if parent is at index $i$, left child is at $2i+1$ and right child is at $2i+2$. At each level, assign indices starting from 0 by subtracting the level's minimum index `minIdx = q.front().second` (`normIdx = currIdx - minIdx`). Width of level $= lastIdx - firstIdx + 1$. Normalizing prevents 64-bit integer overflow on deep skewed trees!

- **Underlying Pattern**: `Complete Binary Tree 0-Indexed Level-Normalized BFS (`2i + 1, 2i + 2`)`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Standard BFS without index normalization (causes integer overflow on tree depth $\ge 64$).

### C++17 Code
```cpp
// Un-normalized BFS (overflows)
```

### Java Code
```java
// Java equivalent
// Un-normalized BFS (overflows)
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$.
- **Space Complexity**: $\mathcal{O}(W)$.
- **Why it's not good enough**: Fails due to integer overflow.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard BFS / DFS algorithm below directly achieves optimal $\mathcal{O}(N)$ or $\mathcal{O}((\log N)^2)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Level-Normalized 0-Indexed BFS in $\mathcal{O}(N)$ time and $\mathcal{O}(W)$ space.

### C++17 Code
```cpp
#include <algorithm>
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
    int widthOfBinaryTree(TreeNode* root) {
        if (root == nullptr) return 0;
        
        long long maxWidth = 0;
        // Queue stores: {TreeNode*, 0-based normalized index}
        queue<pair<TreeNode*, long long>> q;
        q.push({root, 0});
        
        while (!q.empty()) {
            int size = q.size();
            long long minIndex = q.front().second; // minimum index at current level
            long long firstIdx = 0, lastIdx = 0;
            
            for (int i = 0; i < size; i++) {
                auto [curr, idx] = q.front();
                q.pop();
                
                // Normalize index to prevent 64-bit overflow
                long long normIdx = idx - minIndex;
                if (i == 0) firstIdx = normIdx;
                if (i == size - 1) lastIdx = normIdx;
                
                if (curr->left != nullptr) {
                    q.push({curr->left, 2 * normIdx + 1});
                }
                if (curr->right != nullptr) {
                    q.push({curr->right, 2 * normIdx + 2});
                }
            }
            
            maxWidth = max(maxWidth, lastIdx - firstIdx + 1);
        }
        
        return (int)maxWidth;
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

    int widthOfBinaryTree(TreeNode  root) {
        if (root == null) return 0;
        
        long maxWidth = 0;
        // Queue stores: {TreeNode , 0-based normalized index}
        queue<pair<TreeNode , long>> q;
        q.push({root, 0});
        
        while (!q.isEmpty()) {
            int size = q.length;
            long minIndex = q.peek().second; // minimum index at current level
            long firstIdx = 0, lastIdx = 0;
            
            for (int i = 0; i < size; i++) {
                var [curr, idx] = q.peek();
                q.pop();
                
                // Normalize index to prevent 64-bit overflow
                long normIdx = idx - minIndex;
                if (i == 0) firstIdx = normIdx;
                if (i == size - 1) lastIdx = normIdx;
                
                if (curr.left != null) {
                    q.push({curr.left, 2 * normIdx + 1});
                }
                if (curr.right != null) {
                    q.push({curr.right, 2 * normIdx + 2});
                }
            }
            
            maxWidth = Math.max(maxWidth, lastIdx - firstIdx + 1);
        }
        
        return (int)maxWidth;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single BFS pass.
- **Space Complexity**: $\mathcal{O}(W)$ queue memory.
- **Why this is optimal**: Level normalization `idx - minIndex` guarantees indices start at 0 on every level, eliminating overflow risk.

---

## 6. Dry Run

Tree: `1(0) -> left: 3(1) (left: 5(3), right: 3(4)), right: 2(2) (right: 9(6))`

| Step | Action / State Change | Result |
|---|---|---|
| `Level 0` | minIdx = 0 -> root idx = 0 -> width = 0-0+1 = 1 | maxWidth = 1 |
| `Level 1` | minIdx = 1 -> node 3(idx 0), node 2(idx 1) -> width = 1-0+1 = 2 | maxWidth = 2 |
| `Level 2` | minIdx = 3 -> node 5(idx 0), node 3(idx 1), node 9(idx 3) -> width = 3-0+1 = 4 | maxWidth = 4 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single node tree (returns 1).
- Degenerate tree of height 100 (level normalization avoids $2^{100}$ overflow!).

### Common Bugs to Avoid
- Not subtracting `minIndex` before multiplying by 2 (causes rapid overflow into negative numbers after depth 31/63).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does 2 * idx + 1 overflow without level normalization?**  
  **A**: In a skewed tree of depth 64, indices grow exponentially as $2^d$. At depth 64, index exceeds $2^{64}$, overflowing standard `long long`. Subtracting the minimum index at each level keeps values small and relative!

- **Q2: How does width definition differ from just counting non-null nodes?**  
  **A**: Width includes the phantom null nodes that WOULD exist between the leftmost and rightmost nodes in a complete binary tree.

- **Q3: Can DFS solve this problem?**  
  **A**: Yes! Maintain an array/map of `firstIndexAtLevel[level]`. In DFS, `maxWidth = max(maxWidth, currIndex - firstIndexAtLevel[level] + 1)`.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `BFS`, `Queue`, `LeetCode-662`, `Medium`
- **Related problems to practice next**:
- **Level Order Traversal**: Standard BFS.
- **Count Complete Tree Nodes**: CBT properties.
