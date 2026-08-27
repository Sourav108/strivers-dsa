# Minimum Time Taken to BURN the Binary Tree from a Node (Step 13.3 — Hard Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Minimum Time Taken to BURN the Binary Tree from a Node](https://takeuforward.org/data-structure/minimum-time-taken-to-burn-the-binary-tree-from-a-node/)
- **Difficulty**: Hard
- **Statement**: Given a binary tree and a target node `start`, determine the minimum time (in seconds) required to burn the complete binary tree if fire spreads from a burnt node to all adjacent connected nodes (left child, right child, and parent) in 1 second.

---

## 1. Problem, Restated

Find the maximum radial distance from `target` node to any node in the tree using Parent Mapping + BFS.

- **Input**: `TreeNode* root` pointer to binary tree.
- **Output**: Integer width / count / list of nodes / boolean.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) Map parent pointers `unordered_map<TreeNode*, TreeNode*> parentMap` and locate `target` node in initial BFS. 2) Start fire spread BFS from `target` using a `visited` set. 3) In each second, burn all unburnt adjacent neighbors (left, right, parent). Increment `time++` only if at least one new node caught fire. Return `time`.

- **Underlying Pattern**: `Radial BFS Fire Spread Simulation with Incremental Timer`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Build full adjacency graph and run Dijkstra / All-Pairs BFS in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
// Graph BFS approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Graph conversion overhead.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard BFS / DFS algorithm below directly achieves optimal $\mathcal{O}(N)$ or $\mathcal{O}((\log N)^2)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Parent Map + Fire Spread BFS in $\mathcal{O}(N)$ time and $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
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
    TreeNode* mapParentsAndFindTarget(TreeNode* root, unordered_map<TreeNode*, TreeNode*>& parentMap, int start) {
        queue<TreeNode*> q;
        q.push(root);
        TreeNode* targetNode = nullptr;
        
        while (!q.empty()) {
            TreeNode* curr = q.front();
            q.pop();
            
            if (curr->val == start) targetNode = curr;
            
            if (curr->left != nullptr) {
                parentMap[curr->left] = curr;
                q.push(curr->left);
            }
            if (curr->right != nullptr) {
                parentMap[curr->right] = curr;
                q.push(curr->right);
            }
        }
        
        return targetNode;
    }

public:
    int minTimeToBurn(TreeNode* root, int start) {
        if (root == nullptr) return 0;
        
        unordered_map<TreeNode*, TreeNode*> parentMap;
        TreeNode* target = mapParentsAndFindTarget(root, parentMap, start);
        if (target == nullptr) return 0;
        
        queue<TreeNode*> q;
        unordered_set<TreeNode*> visited;
        
        q.push(target);
        visited.insert(target);
        int time = 0;
        
        while (!q.empty()) {
            int size = q.size();
            bool burnedNewNode = false;
            
            for (int i = 0; i < size; i++) {
                TreeNode* curr = q.front();
                q.pop();
                
                // Spread Left
                if (curr->left && visited.find(curr->left) == visited.end()) {
                    burnedNewNode = true;
                    visited.insert(curr->left);
                    q.push(curr->left);
                }
                
                // Spread Right
                if (curr->right && visited.find(curr->right) == visited.end()) {
                    burnedNewNode = true;
                    visited.insert(curr->right);
                    q.push(curr->right);
                }
                
                // Spread Up (Parent)
                if (parentMap.count(curr) && visited.find(parentMap[curr]) == visited.end()) {
                    burnedNewNode = true;
                    visited.insert(parentMap[curr]);
                    q.push(parentMap[curr]);
                }
            }
            
            if (burnedNewNode) {
                time++;
            }
        }
        
        return time;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ parent map, queue, and visited set.
- **Why this is optimal**: BFS visits each node at its exact minimum burn time.

---

## 6. Dry Run

Tree: `1 -> left: 2 (left: 4 (right: 7)), right: 3 (left: 5, right: 6)`, start = 2

| Step | Action / State Change | Result |
|---|---|---|
| `t = 0` | Start burning at node 2 | q: `[2]` |
| `t = 1` | Burns 4 (left), 1 (parent) -> burnedNew=true | time = 1, q: `[4, 1]` |
| `t = 2` | Burns 7 (from 4), 3 (from 1) -> burnedNew=true | time = 2, q: `[7, 3]` |
| `t = 3` | Burns 5, 6 (from 3) -> burnedNew=true | time = 3, q: `[5, 6]` |
| `t = 4` | No unburnt neighbors -> terminates | Total Time = 3 seconds ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single node tree (time = 0).
- Target is leaf node.

### Common Bugs to Avoid
- Incrementing `time++` when no new nodes were burned on final level (causes off-by-one overcounting).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is bool burnedNewNode flag required?**  
  **A**: Because when the last layer of nodes is dequeued, no new unburned neighbors exist. Incrementing `time++` unconditionally would add an extra phantom second after the entire tree is already burnt!

- **Q2: Can this be solved using recursive DFS in O(1) extra space?**  
  **A**: Yes! Compute subtree heights and calculate distance to target during stack unwinding: `maxTime = max(maxTime, distToTarget + heightOfOppositeSubtree)` in $\mathcal{O}(N)$ time and $\mathcal{O}(H)$ stack space.

- **Q3: What is the maximum possible time to burn a tree of N nodes?**  
  **A**: For a linear skewed tree where the target is at one end, time is $N - 1$ seconds.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `BFS`, `Graph`, `Hard`
- **Related problems to practice next**:
- **Nodes at Distance K**: Radial BFS.
- **Diameter of Binary Tree**: Tree diameter.
