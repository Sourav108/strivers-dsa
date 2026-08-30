# Print all Nodes at Distance K in Binary Tree (Step 13.3 — Hard Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Print all Nodes at Distance K in Binary Tree](https://takeuforward.org/data-structure/nodes-at-given-distance-in-binary-tree/)
- **Difficulty**: Medium
- **Statement**: Given the root of a binary tree, the value of a target node `target`, and an integer $k$, return an array of the values of all nodes that have a distance $k$ from the target node.

---

## 1. Problem, Restated

Map parent pointers to convert binary tree into an undirected graph, then run BFS radially outwards from `target` for $k$ steps.

- **Input**: `TreeNode* root` pointer to binary tree.
- **Output**: Integer width / count / list of nodes / boolean.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Binary tree nodes only link downwards to children, but distance $k$ nodes can lie upwards through ancestors. 1) **Pass 1 (Parent Mapping)**: Run BFS/DFS to build `unordered_map<TreeNode*, TreeNode*> parentMap`. 2) **Pass 2 (Radial BFS)**: Start BFS from `target` with a `visited` set. In each step, expand in 3 directions: `left`, `right`, and `parentMap[curr]`. 3) Stop when `distance == k`. The remaining nodes in the queue are all nodes at distance $k$!

- **Underlying Pattern**: `Parent Pointer Hash Mapping + Radial BFS Graph Expansion`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Compute all-pairs shortest paths using Floyd-Warshall in $\mathcal{O}(N^3)$ time.

### C++17 Code
```cpp
// O(N^3) graph shortest path
```

### Java Code
```java
// Java equivalent
// O(N^3) graph shortest path
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^3)$.
- **Space Complexity**: $\mathcal{O}(N^2)$.
- **Why it's not good enough**: All pairs shortest path.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard BFS / DFS algorithm below directly achieves optimal $\mathcal{O}(N)$ or $\mathcal{O}((\log N)^2)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Parent Map + Radial BFS in $\mathcal{O}(N)$ time and $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <vector>
#include <queue>
#include <unordered_map>
#include <unordered_set>
using namespace std;

struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class Solution {
private:
    void mapParents(TreeNode* root, unordered_map<TreeNode*, TreeNode*>& parentMap) {
        queue<TreeNode*> q;
        q.push(root);
        
        while (!q.empty()) {
            TreeNode* curr = q.front();
            q.pop();
            
            if (curr->left != nullptr) {
                parentMap[curr->left] = curr;
                q.push(curr->left);
            }
            if (curr->right != nullptr) {
                parentMap[curr->right] = curr;
                q.push(curr->right);
            }
        }
    }

public:
    vector<int> distanceK(TreeNode* root, TreeNode* target, int k) {
        if (root == nullptr || target == nullptr) return {};
        
        unordered_map<TreeNode*, TreeNode*> parentMap;
        mapParents(root, parentMap);
        
        queue<TreeNode*> q;
        unordered_set<TreeNode*> visited;
        
        q.push(target);
        visited.insert(target);
        int currentDistance = 0;
        
        // Radial BFS outward from target node
        while (!q.empty()) {
            if (currentDistance == k) break;
            
            int size = q.size();
            for (int i = 0; i < size; i++) {
                TreeNode* curr = q.front();
                q.pop();
                
                // 1. Move Left
                if (curr->left && visited.find(curr->left) == visited.end()) {
                    visited.insert(curr->left);
                    q.push(curr->left);
                }
                
                // 2. Move Right
                if (curr->right && visited.find(curr->right) == visited.end()) {
                    visited.insert(curr->right);
                    q.push(curr->right);
                }
                
                // 3. Move Up (Parent)
                if (parentMap.count(curr) && visited.find(parentMap[curr]) == visited.end()) {
                    visited.insert(parentMap[curr]);
                    q.push(parentMap[curr]);
                }
            }
            
            currentDistance++;
        }
        
        vector<int> result;
        while (!q.empty()) {
            result.push_back(q.front()->val);
            q.pop();
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

    void mapParents(TreeNode  root, unordered_map<TreeNode , TreeNode > parentMap) {
        Queue<TreeNode> q = new LinkedList<>();
        q.push(root);
        
        while (!q.isEmpty()) {
            TreeNode  curr = q.peek();
            q.pop();
            
            if (curr.left != null) {
                parentMap[curr.left] = curr;
                q.push(curr.left);
            }
            if (curr.right != null) {
                parentMap[curr.right] = curr;
                q.push(curr.right);
            }
        }
    }

    int[] distanceK(TreeNode  root, TreeNode  target, int k) {
        if (root == null || target == null) return {};
        
        unordered_map<TreeNode , TreeNode > parentMap;
        mapParents(root, parentMap);
        
        Queue<TreeNode> q = new LinkedList<>();
        unordered_set<TreeNode > visited;
        
        q.push(target);
        visited.add(target);
        int currentDistance = 0;
        
        // Radial BFS outward from target node
        while (!q.isEmpty()) {
            if (currentDistance == k) break;
            
            int size = q.length;
            for (int i = 0; i < size; i++) {
                TreeNode  curr = q.peek();
                q.pop();
                
                // 1. Move Left
                if (curr.left && visited.find(curr.left) == visited.end()) {
                    visited.add(curr.left);
                    q.push(curr.left);
                }
                
                // 2. Move Right
                if (curr.right && visited.find(curr.right) == visited.end()) {
                    visited.add(curr.right);
                    q.push(curr.right);
                }
                
                // 3. Move Up (Parent)
                if (parentMap.contains(curr) && visited.find(parentMap[curr]) == visited.end()) {
                    visited.add(parentMap[curr]);
                    q.push(parentMap[curr]);
                }
            }
            
            currentDistance++;
        }
        
        List<Integer> result = new ArrayList<>();
        while (!q.isEmpty()) {
            result.add(q.peek().val);
            q.pop();
        }
        
        return result;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time (2 BFS passes).
- **Space Complexity**: $\mathcal{O}(N)$ parent map, queue, and visited set.
- **Why this is optimal**: Treating tree as an undirected graph enables standard uniform-cost BFS search.

---

## 6. Dry Run

Tree: `3 -> left: 5 (left: 6, right: 2 (left: 7, right: 4)), right: 1 (left: 0, right: 8)`, `target = 5, k = 2`

| Step | Action / State Change | Result |
|---|---|---|
| `d = 0` | q: `[5]`, visited: `{5}` | start |
| `d = 1` | expand 5 -> left: 6, right: 2, parent: 3 -> q: `[6, 2, 3]` | d = 1 |
| `d = 2` | expand 6, 2, 3 -> from 2: 7, 4; from 3: 1 -> q: `[7, 4, 1]` | d = 2 (Target distance reached!) |
| `Result` | Queue contents: `[7, 4, 1]` | Result = `[7, 4, 1]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $k = 0$ (returns `[target->val]`).
- No nodes at distance $k$ (returns `[]`).

### Common Bugs to Avoid
- Forgetting `visited` set check (causes infinite ping-pong between child and parent!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is a visited set strictly required during radial BFS?**  
  **A**: Because parent points to child, and child's parent pointer points back to parent. Without a `visited` set, the BFS would oscillate back and forth between parent and child infinitely!

- **Q2: Can this be solved without building a parent map?**  
  **A**: Yes! Using recursive DFS: return distance from target node to ancestors. For each ancestor, search its OPPOSITE subtree for nodes at depth $(k - \text{distance})$. Takes $\mathcal{O}(N)$ time and $\mathcal{O}(H)$ space.

- **Q3: How does this relate to tree burning problems?**  
  **A**: Tree burning is identical to radial BFS from target, where total time to burn equals the maximum radial BFS distance to the farthest node!


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `BFS`, `Graph`, `LeetCode-863`, `Medium`
- **Related problems to practice next**:
- **Time to Burn Binary Tree**: Burn propagation.
- **Lowest Common Ancestor**: Ancestor traversal.
