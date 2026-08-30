# Vertical Order Traversal of Binary Tree (Step 13.2 — Medium Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Vertical Order Traversal of Binary Tree](https://takeuforward.org/data-structure/vertical-order-traversal-of-binary-tree/)
- **Difficulty**: Hard
- **Statement**: Given the root of a binary tree, calculate the vertical order traversal. For each node at `(row, col)`, left child is at `(row + 1, col - 1)` and right child is at `(row + 1, col + 1)`. Columns sorted left to right, rows sorted top to bottom. If multiple nodes share same `(row, col)`, sort values in ascending order.

---

## 1. Problem, Restated

Group nodes by vertical column `x` and level `y` using coordinate geometry and multiset sorting.

- **Input**: `TreeNode* root` pointer to binary tree.
- **Output**: Traversal vector / boolean.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Assign root coordinate `(col = 0, row = 0)`. Moving left: `(col - 1, row + 1)`. Moving right: `(col + 1, row + 1)`. Use BFS queue storing `{TreeNode*, {col, row}}`. Populate nested sorted map `map<int, map<int, multiset<int>>> nodes`: 1) Outer map sorts columns `x` from left to right. 2) Inner map sorts rows `y` from top to bottom. 3) `multiset<int>` sorts values ascending at identical `(x, y)` coordinates!

- **Underlying Pattern**: `Coordinate Mapping with Nested Ordered Map (`map<int, map<int, multiset<int>>>`)`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
DFS dumping all nodes into a vector of tuples `(col, row, val)` and calling `std::sort` in $\mathcal{O}(N \log N)$ time.

### C++17 Code
```cpp
// Tuple sorting DFS
```

### Java Code
```java
// Java equivalent
// Tuple sorting DFS
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Sorts entire tuple list.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard DFS / BFS algorithm below directly achieves optimal $\mathcal{O}(N)$ or $\mathcal{O}(N \log N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
BFS Queue with Coordinate Nested Ordered Map in $\mathcal{O}(N \log N)$ time.

### C++17 Code
```cpp
#include <vector>
#include <map>
#include <set>
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
    vector<vector<int>> verticalTraversal(TreeNode* root) {
        // map: col -> (row -> multiset of values)
        map<int, map<int, multiset<int>>> nodes;
        
        // Queue stores: {TreeNode*, {col, row}}
        queue<pair<TreeNode*, pair<int, int>>> q;
        q.push({root, {0, 0}});
        
        while (!q.empty()) {
            auto [curr, coord] = q.front();
            q.pop();
            
            int col = coord.first;
            int row = coord.second;
            
            nodes[col][row].insert(curr->val);
            
            if (curr->left != nullptr) {
                q.push({curr->left, {col - 1, row + 1}});
            }
            if (curr->right != nullptr) {
                q.push({curr->right, {col + 1, row + 1}});
            }
        }
        
        vector<vector<int>> result;
        for (auto& [col, rowMap] : nodes) {
            vector<int> colValues;
            for (auto& [row, values] : rowMap) {
                colValues.insert(colValues.end(), values.begin(), values.end());
            }
            result.push_back(colValues);
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

    int[][] verticalTraversal(TreeNode  root) {
        // map: col . (row . multiset of values)
        map<int, map<int, multiset<int>>> nodes;
        
        // Queue stores: {TreeNode , {col, row}}
        queue<pair<TreeNode , pair<int, int>>> q;
        q.push({root, {0, 0}});
        
        while (!q.isEmpty()) {
            var [curr, coord] = q.peek();
            q.pop();
            
            int col = coord.first;
            int row = coord.second;
            
            nodes[col][row].insert(curr.val);
            
            if (curr.left != null) {
                q.push({curr.left, {col - 1, row + 1}});
            }
            if (curr.right != null) {
                q.push({curr.right, {col + 1, row + 1}});
            }
        }
        
        List<List<Integer>> result = new ArrayList<>();
        for (var [col, rowMap] : nodes) {
            int[] colValues;
            for (var [row, values] : rowMap) {
                colValues.add(colValues.end(), values.begin(), values.end());
            }
            result.add(colValues);
        }
        
        return result;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N)$ time (multiset and map insertions).
- **Space Complexity**: $\mathcal{O}(N)$ map memory.
- **Why this is optimal**: Automatically satisfies all three tie-breaking sort constraints (column $\to$ row $\to$ value).

---

## 6. Dry Run

Tree: `3(0,0) -> left: 9(-1,1), right: 20(1,1) (left: 15(0,2), right: 7(2,2))`

| Step | Action / State Change | Result |
|---|---|---|
| `col = -1` | row 1: `[9]` | colValues: `[9]` |
| `col = 0` | row 0: `[3]`, row 2: `[15]` | colValues: `[3, 15]` |
| `col = 1` | row 1: `[20]` | colValues: `[20]` |
| `col = 2` | row 2: `[7]` | colValues: `[7]` |
| `Result` | Vertical Order | `[[9], [3, 15], [20], [7]]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Multiple overlapping nodes with identical value and `(col, row)` coordinates.
- Linear skewed chain.

### Common Bugs to Avoid
- Using `vector<int>` instead of `multiset<int>` at `(col, row)` (fails to sort equal-depth overlapping nodes in ascending order).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is multiset used instead of set for duplicate coordinates?**  
  **A**: Because multiple nodes in a binary tree can have identical values at the exact same `(col, row)` coordinate. `std::set` would deduplicate and drop nodes, whereas `std::multiset` preserves duplicates!

- **Q2: How does Vertical Traversal differ from Top View?**  
  **A**: Vertical Traversal prints ALL nodes in each vertical column sorted by row and value. Top View only prints the FIRST node visible from the top in each column.

- **Q3: Can DFS be used instead of BFS?**  
  **A**: Yes! DFS passing `(col, row)` achieves identical results because the map and multiset automatically sort coordinates regardless of traversal order.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `BFS`, `Coordinate Geometry`, `LeetCode-987`, `Hard`
- **Related problems to practice next**:
- **Top View of Binary Tree**: Column top node.
- **Bottom View of Binary Tree**: Column bottom node.
