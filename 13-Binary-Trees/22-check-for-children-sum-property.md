# Check for Children Sum Property in Binary Tree (Step 13.2 — Medium Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Check for Children Sum Property in Binary Tree](https://takeuforward.org/data-structure/check-for-children-sum-property-in-a-binary-tree/)
- **Difficulty**: Medium
- **Statement**: Given a binary tree, write a function to modify it in-place so that every node satisfies the Children Sum Property: `node->val == (node->left ? node->left->val : 0) + (node->right ? node->right->val : 0)`. You can only INCREMENT node values (decrements not allowed). Also check if a tree satisfies the property.

---

## 1. Problem, Restated

Top-down value propagation + Bottom-up sum aggregation to enforce `root = left + right` without decreasing any value.

- **Input**: `TreeNode* root` pointer to binary tree.
- **Output**: Integer width / count / list of nodes / boolean.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) **Top-Down Phase**: Compute `childSum = (left ? left->val : 0) + (right ? right->val : 0)`. If `childSum >= root->val`, update `root->val = childSum`. If `childSum < root->val`, boost both children to match parent: `if (left) left->val = root->val; if (right) right->val = root->val;`. 2) **Recursive Descent**: Call `changeTree(root->left)` and `changeTree(root->right)`. 3) **Bottom-Up Phase**: Set `root->val = (left ? left->val : 0) + (right ? right->val : 0)`.

- **Underlying Pattern**: `Top-Down Parent Value Boosting + Bottom-Up Children Sum Aggregation`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Repeatedly searching and bumping values in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
// O(N^2) repeated bumps
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$.
- **Space Complexity**: $\mathcal{O}(H)$.
- **Why it's not good enough**: Multiple passes.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard BFS / DFS algorithm below directly achieves optimal $\mathcal{O}(N)$ or $\mathcal{O}((\log N)^2)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Two-Phase Top-Down Boosting + Bottom-Up Aggregation in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class Solution {
public:
    // Transformation: Enforce Children Sum Property by incrementing values
    void changeTree(TreeNode* root) {
        if (root == nullptr) return;
        
        // Step 1: Calculate sum of children
        int childSum = 0;
        if (root->left != nullptr) childSum += root->left->val;
        if (root->right != nullptr) childSum += root->right->val;
        
        // Step 2: Boost children if parent is larger to prevent value shortages
        if (childSum >= root->val) {
            root->val = childSum;
        } else {
            if (root->left != nullptr) root->left->val = root->val;
            if (root->right != nullptr) root->right->val = root->val;
        }
        
        // Step 3: Recurse on subtrees
        changeTree(root->left);
        changeTree(root->right);
        
        // Step 4: Bottom-up aggregation (set root equal to actual resolved children sum)
        int totalSum = 0;
        if (root->left != nullptr) totalSum += root->left->val;
        if (root->right != nullptr) totalSum += root->right->val;
        if (root->left != nullptr || root->right != nullptr) {
            root->val = totalSum;
        }
    }
    
    // Verification: Check if binary tree satisfies Children Sum Property
    bool isSumProperty(TreeNode* root) {
        if (root == nullptr || (root->left == nullptr && root->right == nullptr)) {
            return true; // leaf nodes trivially satisfy property
        }
        
        int sum = 0;
        if (root->left != nullptr) sum += root->left->val;
        if (root->right != nullptr) sum += root->right->val;
        
        return (root->val == sum) && isSumProperty(root->left) && isSumProperty(root->right);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single DFS pass.
- **Space Complexity**: $\mathcal{O}(H)$ call stack space.
- **Why this is optimal**: Boosting children top-down guarantees sufficient value mass for bottom-up aggregation without ever needing to decrement.

---

## 6. Dry Run

Tree: `2 -> left: 35 (left: 2, right: 3), right: 10 (left: 5, right: 2)`

| Step | Action / State Change | Result |
|---|---|---|
| `Top-Down root 2` | childSum = 35+10 = 45 > 2 -> root = 45 | root = 45 |
| `Left child 35` | childSum = 2+3 = 5 < 35 -> boost children: left=35, right=35 | left children boosted |
| `Bottom-up left` | 35 becomes 35+35 = 70 | left = 70 |
| `Bottom-up right` | 10 boosted -> becomes 10+10 = 20 | right = 20 |
| `Bottom-up root` | root = 70 + 20 = 90 | Valid Tree ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single node / Leaf node (trivially valid, no children to sum).
- `root == nullptr`.

### Common Bugs to Avoid
- Decreasing parent node value when `childSum < root->val` (problem explicitly forbids decrementing node values).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why must we boost children to parent value instead of reducing parent value?**  
  **A**: Because the problem constraint stipulates that values can ONLY be incremented, never decremented. Boosting children ensures that child values are large enough to sum up to at least the parent!

- **Q2: Why is the leaf node check if (root->left || root->right) essential in Step 4?**  
  **A**: Because a leaf node has no children (`totalSum = 0`). Overwriting a leaf's value with 0 would destroy all boosted values!

- **Q3: What is the real-world analog of the Children Sum Property?**  
  **A**: Merkle Trees (used in Git and blockchains) where a parent hash is derived from the concatenation/sum of its children hashes.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `DFS`, `Tree Modification`, `Medium`
- **Related problems to practice next**:
- **Maximum Path Sum**: Subtree sums.
- **Same Tree**: Tree verification.
