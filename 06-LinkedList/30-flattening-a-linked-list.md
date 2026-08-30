# Flattening a LinkedList (Merge two sorted lists pattern) (Step 6.5 — Hard Problems of LL)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Flattening a LinkedList (Merge two sorted lists pattern)](https://takeuforward.org/data-structure/flattening-a-linked-list/)
- **Difficulty**: Hard
- **Statement**: Given a linked list where every node has two pointers: `next` (points to the next list on the right) and `bottom` / `child` (points to a sub-linked list sorted in ascending order downwards). Flatten the list into a single sorted linked list ordered downwards using `bottom` pointers.

---

## 1. Problem, Restated

Recursively merge vertical sorted sublists from right to left using the 2-Way Merge pattern.

- **Input**: Head pointer of LinkedList(s) / parameters.
- **Output**: Modified LinkedList head / queried node.
- **Constraints**: Standard competitive programming limits ($0 \le N \le 10^5$).

---

## 2. Intuition & Pattern

Base case: if `!head || !head->next`, return `head`. Recurrence: recursively flatten the list to the right: `rightHead = flatten(head->next)`. Then merge the current vertical list (`head`) with the flattened right list (`rightHead`) using the standard **Merge Two Sorted Lists** algorithm on `bottom` pointers.

- **Underlying Pattern**: `Recursive Right-to-Left Divide & Conquer + 2-Way Merge`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Collect all node values into a vector, sort with `std::sort`, reconstruct vertical list in $\mathcal{O}(N \log N)$ time and $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;
struct Node { int data; Node* next; Node* bottom; };
Node* flattenBrute(Node* root) {
    vector<int> v;
    Node* r = root;
    while (r) {
        Node* b = r;
        while (b) { v.push_back(b->data); b = b->bottom; }
        r = r->next;
    }
    sort(v.begin(), v.end());
    if (v.empty()) return nullptr;
    Node* head = new Node{v[0], nullptr, nullptr};
    Node* curr = head;
    for (size_t i = 1; i < v.size(); i++) {
        curr->bottom = new Node{v[i], nullptr, nullptr};
        curr = curr->bottom;
    }
    return head;
}
```

### Java Code
```java
import java.util.*;

static class Node { int data; Node  next; Node  bottom; };
Node  flattenBrute(Node  root) {
    List<Integer> v = new ArrayList<>();
    Node  r = root;
    while (r) {
        Node  b = r;
        while (b) { v.add(b.data); b = b.bottom; }
        r = r.next;
    }
    Arrays.sort(v);
    if (v.isEmpty()) return null;
    Node  head = new Node{v[0], null, null};
    Node  curr = head;
    for (int i = 1; i < v.length; i++) {
        curr.bottom = new Node{v[i], null, null};
        curr = curr.bottom;
    }
    return head;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N)$ time where $N$ is total nodes across all chains.
- **Space Complexity**: $\mathcal{O}(N)$ vector storage.
- **Why it's not good enough**: Extra memory.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard pointer manipulation below directly solves the problem.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Recursive Merge on `bottom` pointers in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
struct Node {
    int data;
    Node* next;
    Node* bottom;
    Node(int val) : data(val), next(nullptr), bottom(nullptr) {}
};

class Solution {
private:
    Node* mergeTwoLists(Node* a, Node* b) {
        Node dummy(0);
        Node* res = &dummy;
        
        while (a != nullptr && b != nullptr) {
            if (a->data <= b->data) {
                res->bottom = a;
                res = a;
                a = a->bottom;
            } else {
                res->bottom = b;
                res = b;
                b = b->bottom;
            }
            res->next = nullptr; // clean next pointer
        }
        
        if (a) res->bottom = a;
        else res->bottom = b;
        
        return dummy.bottom;
    }

public:
    Node *flatten(Node *root) {
        // Base case: empty or single vertical list
        if (!root || !root->next) return root;
        
        // Recursively flatten rightward sublists
        root->next = flatten(root->next);
        
        // Merge current vertical list with flattened right list
        root = mergeTwoLists(root, root->next);
        
        return root;
    }
};
```

### Java Code
```java
import java.util.*;

static class Node {
    int data;
    Node  next;
    Node  bottom;
    public Node(int val) { /* initialized: data(val), next(null), bottom(null)  */  }
};

class Solution {

    Node  mergeTwoLists(Node  a, Node  b) {
        Node dummy(0);
        Node  res = &dummy;
        
        while (a != null && b != null) {
            if (a.data <= b.data) {
                res.bottom = a;
                res = a;
                a = a.bottom;
            } else {
                res.bottom = b;
                res = b;
                b = b.bottom;
            }
            res.next = null; // clean next pointer
        }
        
        if (a) res.bottom = a;
        else res.bottom = b;
        
        return dummy.bottom;
    }

    Node flatten(Node root) {
        // Base case: empty or single vertical list
        if (!root || !root.next) return root;
        
        // Recursively flatten rightward sublists
        root.next = flatten(root.next);
        
        // Merge current vertical list with flattened right list
        root = mergeTwoLists(root, root.next);
        
        return root;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time where $N$ is the total count of all nodes.
- **Space Complexity**: $\mathcal{O}(K)$ recursion call stack space where $K$ is the number of vertical head chains.
- **Why this is optimal**: Reuses existing `bottom` node links without allocating new nodes.

---

## 6. Dry Run

Chains: `[5->7->8]` -> `[10->20]` -> `[19->22->50]`

| Step | Action / State Change | Result |
|---|---|---|
| `Step 1` | flatten(`[10->20]`, `[19->22->50]`) | Merged: `[10->19->20->22->50]` |
| `Step 2` | merge(`[5->7->8]`, `[10->19->20->22->50]`) | Final: `[5->7->8->10->19->20->22->50]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single vertical chain (returns root).
- Empty chains.

### Common Bugs to Avoid
- Leaving dangling `next` pointers (must clear `res->next = nullptr`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Can this be solved using a Min-Heap / Priority Queue?**  
  **A**: Yes! Insert all vertical head nodes into a min-heap. Pop smallest node, link it, and push its `bottom` child. Time: $\mathcal{O}(N \log K)$, space: $\mathcal{O}(K)$.


---

## 9. Tags & Related Problems

- **Tags**: `LinkedList`, `Divide and Conquer`, `Merge`, `Hard`
- **Related problems to practice next**:
- **Merge K Sorted Lists**: Heap merge pattern.
