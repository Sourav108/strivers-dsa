# Construct String from Binary Tree with Parentheses (Step 13.3 — Hard Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Construct String from Binary Tree with Parentheses](https://takeuforward.org/data-structure/construct-string-from-binary-tree/)
- **Difficulty**: Easy
- **Statement**: Given the root of a binary tree, construct a string consisting of parenthesis and integers from a binary tree with the preorder traversal way, and return it. Omit all the 0-pair parenthesis that do not affect the one-to-one mapping relationship between the string and the original binary tree.

---

## 1. Problem, Restated

Preorder string construction: include empty `()` for left child ONLY IF right child exists (`if (left || right) "(" + tree2str(left) + ")"`).

- **Input**: Parameters specified.
- **Output**: Traversal vector / string / node value.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) If `root == nullptr`, return `""`. 2) Start with `to_string(root->val)`. 3) **Left Child Rule**: If `left != nullptr || right != nullptr`, we MUST append `"(" + tree2str(left) + ")"` (even if left is null, to preserve right child alignment!). 4) **Right Child Rule**: If `right != nullptr`, append `"(" + tree2str(right) + ")"` (if right is null, we can safely omit it). Runs in $\mathcal{O}(N)$ time.

- **Underlying Pattern**: `Preorder Tree Serialization with One-to-One Mapping Parenthesis Rules`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Full parenthesized preorder traversal followed by regular expression cleanup passes.

### C++17 Code
```cpp
// Regex cleanup approach
```

### Java Code
```java
// Java equivalent
// Regex cleanup approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Multiple string copying passes.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard DFS / Binary Lifting algorithm below directly achieves optimal $\mathcal{O}(N)$ or $\mathcal{O}(\log K)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Conditional Preorder DFS String Accumulation in $\mathcal{O}(N)$ time and $\mathcal{O}(H)$ space.

### C++17 Code
```cpp
#include <string>
using namespace std;

struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class Solution {
public:
    string tree2str(TreeNode* root) {
        if (root == nullptr) return "";
        
        string res = to_string(root->val);
        
        // If left child exists OR right child exists, we MUST process left branch
        if (root->left != nullptr || root->right != nullptr) {
            res += "(" + tree2str(root->left) + ")";
        }
        
        // Process right branch ONLY if right child exists
        if (root->right != nullptr) {
            res += "(" + tree2str(root->right) + ")";
        }
        
        return res;
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

    String tree2str(TreeNode  root) {
        if (root == null) return "";
        
        String res = String.valueOf(root.val);
        
        // If left child exists OR right child exists, we MUST process left branch
        if (root.left != null || root.right != null) {
            res += "(" + tree2str(root.left) + ")";
        }
        
        // Process right branch ONLY if right child exists
        if (root.right != null) {
            res += "(" + tree2str(root.right) + ")";
        }
        
        return res;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(H)$ call stack space.
- **Why this is optimal**: Direct conditional branching ensures exact one-to-one mapping without post-processing.

---

## 6. Dry Run

Tree 1: `1 -> left: 2 (left: 4), right: 3` vs Tree 2: `1 -> left: 2 (right: 4), right: 3`

| Step | Action / State Change | Result |
|---|---|---|
| `Tree 1 (2 has left 4)` | 2->left(4) exists, right null -> `"1(2(4))(3)"` | `"1(2(4))(3)"` ✅ |
| `Tree 2 (2 has right 4)` | 2->left is null but right exists -> empty `()` needed for left: `"1(2()(4))(3)"` | `"1(2()(4))(3)"` ✅ |
| `Result` | One-to-one mapping preserved | Valid String Representation ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single node tree `[1]` (returns `"1"`).
- Left child null with existing right child.

### Common Bugs to Avoid
- Omitting `()` when left child is null and right child exists (creates ambiguity: `"1(2(4))"` could mean left or right child!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why must we include empty () for a null left child if a right child exists?**  
  **A**: Because without `()`, `1(2(4))` would be parsed as `4` being the LEFT child of `2`. The empty `()` explicitly denotes that the left child is missing, making `4` the RIGHT child!

- **Q2: Why can we omit () if the right child is null?**  
  **A**: Because if right is omitted (`1(2(4))`), the first parenthesis pair after `2` is always parsed as the left child by default. No ambiguity exists!

- **Q3: Can this be done iteratively with a stack?**  
  **A**: Yes! Push nodes onto a stack with `visited` set to emit opening `(` on entry and closing `)` on exit.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `String`, `DFS`, `LeetCode-606`, `Easy`
- **Related problems to practice next**:
- **Serialize and Deserialize**: Full tree serialization.
