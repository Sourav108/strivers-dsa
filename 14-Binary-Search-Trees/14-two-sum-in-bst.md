# Two Sum IV - Input is a BST (Two Pointer with two BST Iterators) (Step 14.2 — Practice Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Two Sum IV - Input is a BST (Two Pointer with two BST Iterators)](https://takeuforward.org/data-structure/two-sum-in-bst/)
- **Difficulty**: Medium
- **Statement**: Given the root of a binary search tree and an integer $k$, return `true` if there exist two elements in the BST such that their sum is equal to $k$, or `false` otherwise.

---

## 1. Problem, Restated

Two Pointers on BST: maintain Forward BST Iterator (`left`) and Reverse BST Iterator (`right`) converging in $\mathcal{O}(N)$ time and $\mathcal{O}(H)$ space.

- **Input**: Parameters specified.
- **Output**: Value / Node pointer / boolean / class methods.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) **Hash Set $\mathcal{O}(N)$ space**: Traverse BST with `unordered_set<int> visited`. If `visited.count(k - node->val)`, return `true`. 2) **Dual BST Iterators $\mathcal{O}(H)$ space**: Create `BSTIterator l(root, false)` (forward iterator producing ascending values) and `BSTIterator r(root, true)` (reverse iterator producing descending values). `i = l.next(), j = r.next()`. While `i < j`: if `i + j == k` return `true`; if `i + j < k`, advance `i = l.next()`; if `i + j > k`, advance `j = r.next()`! Uses only $\mathcal{O}(2H) = \mathcal{O}(H)$ space!

- **Underlying Pattern**: `Dual BST Iterators (Forward & Reverse) Two-Pointer Convergence`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Inorder traversal into a vector, then run two pointers on vector in $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
// Vector two pointer approach
```

### Java Code
```java
// Java equivalent
// Vector two pointer approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ space.
- **Why it's not good enough**: Stores entire tree array.

---

## 4. Approach 2 — Better

### Idea
Hash Set DFS in O(N) time and O(N) hash set space.

### C++17 Code
```cpp
#include <unordered_set>
using namespace std;
struct TreeNode { int val; TreeNode *left, *right; };
class SolutionHashSet {
    unordered_set<int> st;
public:
    bool findTarget(TreeNode* root, int k) {
        if (!root) return false;
        if (st.count(k - root->val)) return true;
        st.insert(root->val);
        return findTarget(root->left, k) || findTarget(root->right, k);
    }
};
```

### Java Code
```java
import java.util.*;

static class TreeNode { int val; TreeNode left, right; };
class SolutionHashSet {
    Set<Integer> st = new HashSet<>();

    boolean findTarget(TreeNode  root, int k) {
        if (root == null) return false;
        if (st.contains(k - root.val)) return true;
        st.add(root.val);
        return findTarget(root.left, k) || findTarget(root.right, k);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ hash set space.
- **Why it's still not optimal**: Consumes linear memory.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Dual BST Iterators in $\mathcal{O}(N)$ time and strict $\mathcal{O}(H)$ space.

### C++17 Code
```cpp
#include <stack>
using namespace std;

struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class BSTIterator {
private:
    stack<TreeNode*> st;
    bool reverse; // false -> next() [ascending]; true -> before() [descending]
    
    void pushAll(TreeNode* node) {
        while (node != nullptr) {
            st.push(node);
            node = reverse ? node->right : node->left;
        }
    }

public:
    BSTIterator(TreeNode* root, bool isReverse) : reverse(isReverse) {
        pushAll(root);
    }
    
    int next() {
        TreeNode* topNode = st.top();
        st.pop();
        if (reverse) {
            pushAll(topNode->left);
        } else {
            pushAll(topNode->right);
        }
        return topNode->val;
    }
};

class Solution {
public:
    bool findTarget(TreeNode* root, int k) {
        if (root == nullptr) return false;
        
        BSTIterator l(root, false); // forward iterator (starts at min)
        BSTIterator r(root, true);  // reverse iterator (starts at max)
        
        int i = l.next();
        int j = r.next();
        
        while (i < j) {
            if (i + j == k) return true;
            if (i + j < k) i = l.next();
            else j = r.next();
        }
        
        return false;
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

class BSTIterator {

    Stack<TreeNode> st = new Stack<>();
    boolean reverse; // false . next() [ascending]; true . before() [descending]
    
    void pushAll(TreeNode  node) {
        while (node != null) {
            st.push(node);
            node = reverse ? node.right : node.left;
        }
    }

    public BSTIterator(TreeNode  root, boolean isReverse) { /* initialized: reverse(isReverse)  */ 
        pushAll(root);
     }
    
    int next() {
        TreeNode  topNode = st.peek();
        st.pop();
        if (reverse) {
            pushAll(topNode.left);
        } else {
            pushAll(topNode.right);
        }
        return topNode.val;
    }
};

class Solution {

    boolean findTarget(TreeNode  root, int k) {
        if (root == null) return false;
        
        BSTIterator l(root, false); // forward iterator (starts at min)
        BSTIterator r(root, true);  // reverse iterator (starts at max)
        
        int i = l.next();
        int j = r.next();
        
        while (i < j) {
            if (i + j == k) return true;
            if (i + j < k) i = l.next();
            else j = r.next();
        }
        
        return false;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time (each node visited at most once).
- **Space Complexity**: $\mathcal{O}(H)$ memory (2 stacks of max depth $H$).
- **Why this is optimal**: Simulates classic Two Sum two-pointer convergence without allocating an $\mathcal{O}(N)$ vector.

---

## 6. Dry Run

BST: `5 -> left: 3 (left: 2, right: 4), right: 6 (right: 7)`, $k = 9$

| Step | Action / State Change | Result |
|---|---|---|
| `Init Iterators` | i = l.next() = 2 (min), j = r.next() = 7 (max) | i=2, j=7 |
| `Check Sum` | $i + j = 2 + 7 = 9 == k(9) \implies$ Pair Found! | Return TRUE ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Target sum equals double a single node's value (e.g. $k = 6$ with single node 3 $\implies$ $i < j$ condition prevents using same node twice!).
- $N = 1$ (returns `false`).

### Common Bugs to Avoid
- Condition `while (i <= j)` instead of `while (i < j)` (falsely allows a single node to sum with itself).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is the space complexity O(H) instead of O(N)?**  
  **A**: Because each iterator only stores the active spine of the tree. For a balanced tree of $10^6$ nodes, height is $\approx 20$, so the iterator uses only 20 stack frames instead of $10^6$ array elements!

- **Q2: Can this be solved in O(1) auxiliary space using Morris Traversal?**  
  **A**: No, because simultaneous forward and reverse Morris traversals would interfere with each other's temporary threaded pointers.

- **Q3: How does BSTIterator handle duplicate values?**  
  **A**: Strict BSTs do not contain duplicates. If duplicates were supported, iterator would yield identical values consecutively.


---

## 9. Tags & Related Problems

- **Tags**: `BST`, `Two Pointers`, `Iterator`, `LeetCode-653`, `Medium`
- **Related problems to practice next**:
- **BST Iterator**: Iterator engine.
- **Two Sum**: Array Two-Sum.
