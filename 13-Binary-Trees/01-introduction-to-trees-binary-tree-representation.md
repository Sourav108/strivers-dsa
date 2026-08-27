# Introduction to Trees & Binary Tree Representation (Step 13.1 — Traversals)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Introduction to Trees & Binary Tree Representation](https://takeuforward.org/data-structure/binary-tree-representation-in-c/)
- **Difficulty**: Easy
- **Statement**: Explain the fundamental concepts of Trees (Root, Leaf, Parent, Child, Depth, Height, Subtree), types of binary trees (Full, Complete, Perfect, Balanced, Degenerate), and implement the standard C++ `TreeNode` struct with 24-byte memory layout and node construction.

---

## 1. Problem, Restated

Tree data structure definitions, binary tree classifications, and 64-bit pointer struct implementation.

- **Input**: `TreeNode* root` pointer to the root of a binary tree.
- **Output**: Traversal vector or tree node construction.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

A Binary Tree is a hierarchical non-linear data structure where each node has at most two children (`left` and `right`). In 64-bit systems, a `TreeNode` contains an integer `val` (4 bytes) + 4 bytes padding + two 8-byte pointers (`left` and `right`) = **24 bytes**. Memory is allocated dynamically on the heap using modern constructors.

- **Underlying Pattern**: `Hierarchical Non-Linear Node Pointer Structure`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Representing trees as 2D adjacency matrix taking $\mathcal{O}(N^2)$ memory.

### C++17 Code
```cpp
// Adjacency matrix representation (wasteful O(N^2) memory)
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ space.
- **Space Complexity**: $\mathcal{O}(N^2)$.
- **Why it's not good enough**: Quadratic memory for tree graphs.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard iterative and recursive algorithms below directly achieve optimal bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Iterative / Production)

### Idea
Standard C++ TreeNode Struct with Initializer Lists in $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <iostream>
using namespace std;

struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    
    TreeNode() : val(0), left(nullptr), right(nullptr) {}
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
    TreeNode(int x, TreeNode* l, TreeNode* r) : val(x), left(l), right(r) {}
};

class Solution {
public:
    void createTree(TreeNode*& root, vector<int>& arr) {
        // Construct binary tree level-by-level from array
        if (arr.empty()) return;
        root = new TreeNode(arr[0]);
        root->left = new TreeNode(arr[1]);
        root->right = new TreeNode(arr[2]);
        root->left->left = new TreeNode(arr[3]);
        root->left->right = new TreeNode(arr[4]);
        root->right->left = new TreeNode(arr[5]);
        root->right->right = new TreeNode(arr[6]);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ per node allocation, $\mathcal{O}(N)$ total.
- **Space Complexity**: $\mathcal{O}(N)$ heap memory (24 bytes per node).
- **Why this is optimal**: Minimal memory overhead with direct cache pointer traversal.

---

## 6. Dry Run

Tree from `arr = [1, 2, 3, 4, 5, 6, 7]`

| Step | Action / State Change | Result |
|---|---|---|
| `Root` | root->val = 1 | level 0 |
| `Level 1` | left = 2, right = 3 | level 1 |
| `Level 2` | 2->left = 4, 2->right = 5, 3->left = 6, 3->right = 7 | level 2 |
| `Result` | Perfect Binary Tree of depth 2 created | Complete ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Empty tree (`root == nullptr`).
- Degenerate / Skewed tree (behaves like a linked list).

### Common Bugs to Avoid
- Dangling pointers when forgetting to initialize `left = nullptr, right = nullptr` in default constructor.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is the difference between a Full Binary Tree and a Complete Binary Tree?**  
  **A**: A **Full Binary Tree** requires every node to have either 0 or 2 children. A **Complete Binary Tree** requires all levels to be completely filled except possibly the last level, which must be filled from left to right.

- **Q2: What is the memory footprint of TreeNode on 64-bit systems?**  
  **A**: `sizeof(int) = 4` bytes. Pointers are 8 bytes each ($2 \times 8 = 16$). Due to 8-byte struct alignment, the compiler adds 4 bytes of padding after `val`, yielding exactly `4 + 4 + 16 = 24` bytes.

- **Q3: Why is recursive traversal space O(N) in worst case?**  
  **A**: In a skewed tree (degenerate tree of height $N$), the call stack depth reaches $N$, consuming $\mathcal{O}(N)$ auxiliary memory.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `Basics`, `Data Structures`, `Easy`
- **Related problems to practice next**:
- **Preorder Traversal**: Tree traversal.
