# Serialize and Deserialize Binary Tree (String formatting) (Step 13.3 — Hard Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Serialize and Deserialize Binary Tree (String formatting)](https://takeuforward.org/data-structure/serialize-and-deserialize-a-binary-tree/)
- **Difficulty**: Hard
- **Statement**: Serialization is the process of converting a data structure or object into a sequence of bits so that it can be stored in a file or memory buffer, or transmitted across a network connection link to be reconstructed later in the same or another computer environment. Design an algorithm to serialize and deserialize a binary tree.

---

## 1. Problem, Restated

Encode binary tree into a comma-separated string using BFS with '#' for nulls, and decode back into identical tree structure.

- **Input**: Traversal arrays / Tree pointers / Serialized strings.
- **Output**: Tree root `TreeNode*` / Serialized string / Traversal vector / boolean.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) **Serialize**: Run standard BFS. Push `root`. For each node: if non-null, append `to_string(node->val) + ","` and enqueue children (even if null!); if null, append `"#,"`. 2) **Deserialize**: Use `stringstream` splitting by `','`. Read root token. Push root into queue. For each node popped from queue: read next token for left child (create node if not `'#'`, enqueue), read next token for right child (create node if not `'#'`, enqueue)!

- **Underlying Pattern**: `Stringstream CSV Tokenized BFS Queue Level Serialization`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Preorder + Inorder serialization (requires building 2 strings and running O(N log N) reconstruction).

### C++17 Code
```cpp
// Preorder + Inorder serialization
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ 2 strings.
- **Why it's not good enough**: Fails if tree has duplicate values.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard divide-and-conquer / string parsing / Morris threading algorithm below directly achieves optimal bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
BFS Queue with Comma Tokenization in $\mathcal{O}(N)$ time and $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <string>
#include <sstream>
#include <queue>
using namespace std;

struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class Codec {
public:
    // Encodes a tree to a single string using BFS
    string serialize(TreeNode* root) {
        if (root == nullptr) return "";
        
        string s = "";
        queue<TreeNode*> q;
        q.push(root);
        
        while (!q.empty()) {
            TreeNode* curr = q.front();
            q.pop();
            
            if (curr == nullptr) {
                s.append("#,");
            } else {
                s.append(to_string(curr->val) + ",");
                q.push(curr->left);
                q.push(curr->right);
            }
        }
        
        return s;
    }

    // Decodes your encoded data to tree using BFS
    TreeNode* deserialize(string data) {
        if (data.empty()) return nullptr;
        
        stringstream s(data);
        string str;
        getline(s, str, ',');
        
        TreeNode* root = new TreeNode(stoi(str));
        queue<TreeNode*> q;
        q.push(root);
        
        while (!q.empty()) {
            TreeNode* curr = q.front();
            q.pop();
            
            // 1. Process Left Child
            getline(s, str, ',');
            if (str != "#") {
                TreeNode* leftNode = new TreeNode(stoi(str));
                curr->left = leftNode;
                q.push(leftNode);
            }
            
            // 2. Process Right Child
            getline(s, str, ',');
            if (str != "#") {
                TreeNode* rightNode = new TreeNode(stoi(str));
                curr->right = rightNode;
                q.push(rightNode);
            }
        }
        
        return root;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time for both serialization and deserialization.
- **Space Complexity**: $\mathcal{O}(N)$ queue and stringstream memory.
- **Why this is optimal**: Explicit null markers `'#'` allow deterministic tree reconstruction from a single traversal pass, natively supporting duplicate node values.

---

## 6. Dry Run

Tree: `1 -> left: 2, right: 3 (left: 4, right: 5)`

| Step | Action / State Change | Result |
|---|---|---|
| `Serialize` | q visits: 1, 2, 3, null, null, 4, 5, null, null, null, null | str: `"1,2,3,#,#,4,5,#,#,#,#,"` |
| `Deserialize Root` | token 1 -> root = 1, q: `[1]` | root created |
| `Children of 1` | tokens 2, 3 -> 1->left = 2, 1->right = 3 -> q: `[2, 3]` | q: `[2, 3]` |
| `Children of 2` | tokens #, # -> left=null, right=null | q: `[3]` |
| `Children of 3` | tokens 4, 5 -> 3->left = 4, 3->right = 5 | Exact Tree Reconstructed ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Empty tree `root == nullptr` (serialized to `""`, deserialized to `nullptr`).
- Negative values and duplicate values in tree.

### Common Bugs to Avoid
- Forgetting delimiter `','` between multi-digit numbers (e.g. `12` and `3` becoming indistinguishable from `1` and `23`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does BFS with '#' null markers work with duplicate values while Preorder+Inorder fails?**  
  **A**: Because explicit null markers encode the exact pointer geometry of every single node in traversal order, removing any ambiguity caused by duplicate numbers!

- **Q2: Can DFS (Preorder) be used instead of BFS?**  
  **A**: Yes! Preorder serialization with null markers (`1,2,#,#,3,4,#,#,5,#,#`) can be deserialized using recursive preorder descent in $\mathcal{O}(N)$ time.

- **Q3: How is binary tree serialization optimized in production systems (Protocol Buffers / FlatBuffers)?**  
  **A**: Using compact bit-packed formats: 1 bit indicating null/non-null child, followed by variable-length zig-zag integer encoding (Varint) for values.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `Design`, `String`, `BFS`, `LeetCode-297`, `Hard`
- **Related problems to practice next**:
- **Construct from Pre & In**: Reconstruction without nulls.
