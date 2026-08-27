# Delete the Middle Node of LinkedList (Step 6.3 — Medium Problems of LL)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Delete the Middle Node of LinkedList](https://takeuforward.org/data-structure/delete-the-middle-node-of-the-linked-list/)
- **Difficulty**: Medium
- **Statement**: Given the head of a linked list, delete the middle node (node $\lfloor N/2 \rfloor$) and return the head of the modified list in a single pass.

---

## 1. Problem, Restated

Position `slow` at predecessor of middle node $\lfloor N/2 \rfloor - 1$ using skip-step fast pointer.

- **Input**: Head pointer of LinkedList(s) / parameters.
- **Output**: Modified LinkedList head / queried node.
- **Constraints**: Standard competitive programming limits ($0 \le N \le 10^5$).

---

## 2. Intuition & Pattern

To delete the middle node without maintaining a separate `prev` pointer, initialize `slow = head` and `fast = head->next->next` (skip 2 steps for fast at start). When `fast` reaches end (`fast == nullptr` or `fast->next == nullptr`), `slow` will be at the node PRECEDING the middle node! Simply set `slow->next = slow->next->next`.

- **Underlying Pattern**: `Tortoise-Hare Predecessor Skipping (`fast = head->next->next`)`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Count length $N$, find $N/2$, delete.

### C++17 Code
```cpp
// Two passes
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Two traversals.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard pointer manipulation below directly solves the problem.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Fast-Slow pointer with 1-step slow offset in $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

class Solution {
public:
    Node* deleteMiddle(Node* head) {
        // Base case: empty or single node list
        if (!head || !head->next) {
            delete head;
            return nullptr;
        }
        
        // Fast starts 2 steps ahead so slow stops at (mid - 1)
        Node* slow = head;
        Node* fast = head->next->next;
        
        while (fast != nullptr && fast->next != nullptr) {
            slow = slow->next;
            fast = fast->next->next;
        }
        
        // Delete middle node
        Node* middleNode = slow->next;
        slow->next = slow->next->next;
        delete middleNode;
        
        return head;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N/2) = \mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Stops slow at $(N/2 - 1)$ in a single traversal.

---

## 6. Dry Run

`[1, 3, 4, 7, 1, 2, 6]` ($N = 7$, mid = index 3 (val 7))

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | slow = [1], fast = [4] | fast is 2 steps ahead |
| `Iter 1` | slow = [3], fast = [1] | In loop |
| `Iter 2` | slow = [4], fast = [6] (fast->next=null) | Loop terminates |
| `Delete` | slow points to [4]. Delete slow->next ([7]) | `[1, 3, 4, 1, 2, 6]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single node list ($N = 1$ -> returns `nullptr`).
- Two node list `[1, 2]` -> deletes `[2]`.

### Common Bugs to Avoid
- Not checking `!head || !head->next` base case.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does starting fast at head->next->next position slow at predecessor?**  
  **A**: Normal fast-slow stops slow at index $N/2$. By advancing fast by 2 steps upfront, slow takes 1 fewer step, stopping exactly at index $N/2 - 1$.


---

## 9. Tags & Related Problems

- **Tags**: `LinkedList`, `Two Pointers`, `Fast Slow`, `LeetCode-2095`, `Medium`
- **Related problems to practice next**:
- **Find Middle Node**: Base mid search.
