# Maximum Path Sum in Binary Tree (Step 13.2 — Medium Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Maximum Path Sum in Binary Tree](https://takeuforward.org/data-structure/maximum-sum-path-in-binary-tree/)
- **Difficulty**: Hard
- **Statement**: A path in a binary tree is a sequence of nodes where each pair of adjacent nodes has an edge connecting them. A node can only appear in the sequence at most once. Return the maximum path sum of any non-empty path.

---

## 1. Problem, Restated

Find the maximum sum path in a binary tree where nodes can have negative values.

- **Input**: `TreeNode* root` pointer to the root of a binary tree.
- **Output**: Traversal vectors or metric calculation (depth, diameter, path sum).
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

At each node $u$: 1) Recursively compute the maximum branch sum from left child `leftGain = max(0, dfs(node->left))` and right child `rightGain = max(0, dfs(node->right))` (clamping negative gains to 0 since negative branches only decrease total sum). 2) The best path TURNING at node $u$ is `node->val + leftGain + rightGain`. Update global `maxSum = max(maxSum, node->val + leftGain + rightGain)`. 3) Return the single best extendable branch to the parent: `node->val + max(leftGain, rightGain)`.

- **Underlying Pattern**: `Bottom-Up Path Branch Maximization with Zero Clamping (`max(0, gain)`)`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Finding paths between all pairs of nodes in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
// O(N^2) path search
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$.
- **Space Complexity**: $\mathcal{O}(H)$.
- **Why it's not good enough**: Quadratic path combinations.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard iterative and recursive algorithms below directly achieve optimal bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Single Bottom-Up DFS with Negative Clamping in $\mathcal{O}(N)$ time and $\mathcal{O}(H)$ space.

### C++17 Code
```cpp
#include <algorithm>
#include <climits>
using namespace std;

struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class Solution {
private:
    int maxGain(TreeNode* node, int& maxSum) {
        if (node == nullptr) return 0;
        
        // Greedily ignore negative subtree contributions by clamping with 0
        int leftGain = max(0, maxGain(node->left, maxSum));
        int rightGain = max(0, maxGain(node->right, maxSum));
        
        // Maximum path sum with current node as the turning apex
        int currentPathSum = node->val + leftGain + rightGain;
        maxSum = max(maxSum, currentPathSum);
        
        // Return maximum single branch path to parent
        return node->val + max(leftGain, rightGain);
    }

public:
    int maxPathSum(TreeNode* root) {
        int maxSum = INT_MIN;
        maxGain(root, maxSum);
        return maxSum;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single DFS pass.
- **Space Complexity**: $\mathcal{O}(H)$ call stack space.
- **Why this is optimal**: Negative branch clamping `max(0, gain)` eliminates unproductive paths in linear time.

---

## 6. Dry Run

Tree: `-10 -> left: 9, right: 20 (left: 15, right: 7)`

| Step | Action / State Change | Result |
|---|---|---|
| `Node 9` | left=0, right=0 -> currentPath = 9+0+0 = 9 -> maxSum = 9, return 9 | return 9 |
| `Node 15, 7` | return 15 and 7 to parent 20 | returns 15, 7 |
| `Node 20` | leftGain=15, rightGain=7 -> path = 20+15+7 = 42 -> maxSum = max(9, 42) = 42, return 20+max(15,7) = 35 | maxSum = 42 |
| `Root -10` | leftGain=9, rightGain=35 -> path = -10 + 9 + 35 = 34 -> maxSum = max(42, 34) = 42 | maxSum = 42 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- All nodes have negative values `[-3]` (returns `-3`, why `maxSum` is initialized to `INT_MIN`).
- Tree with single node.

### Common Bugs to Avoid
- Initializing `maxSum = 0` (fails when all node values in tree are negative, returning 0 instead of the maximum negative value!).
- Returning `node->val + leftGain + rightGain` to the parent (a path to parent can only take ONE branch, not both).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why must we return node->val + max(leftGain, rightGain) instead of node->val + leftGain + rightGain?**  
  **A**: Because a path cannot fork! If a parent node continues the path upwards, it can only choose either the left branch OR the right branch, not both.

- **Q2: Why do we clamp gains with max(0, ...)?**  
  **A**: If a subtree has a net negative sum, including it in our path would only decrease the total sum. Clamping with 0 means we choose not to include that subtree in the path.

- **Q3: How does this problem generalize Kadane's Algorithm to trees?**  
  **A**: Just as Kadane's algorithm resets negative running prefix sums to 0 (`sum = max(0, sum + x)`), `max(0, maxGain)` resets negative subtree branches to 0.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `DFS`, `Dynamic Programming`, `LeetCode-124`, `Hard`
- **Related problems to practice next**:
- **Diameter of Binary Tree**: Unweighted longest path.
- **Path Sum III**: Target sum sub-paths.
