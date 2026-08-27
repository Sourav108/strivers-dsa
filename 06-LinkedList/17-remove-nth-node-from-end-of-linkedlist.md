# Remove Nth Node from End of LinkedList (Step 6.3 — Medium Problems of LL)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Remove Nth Node from End of LinkedList](https://takeuforward.org/data-structure/remove-nth-node-from-back-of-linked-list/)
- **Difficulty**: Medium
- **Statement**: Given the head of a linked list, remove the $N^{\text{th}}$ node from the end of the list and return its head in a single pass.

---

## 1. Problem, Restated

Delete the node at index $(L - N)$ using two pointers separated by distance $N$.

- **Input**: Head pointer of LinkedList(s) / parameters.
- **Output**: Modified LinkedList head / queried node.
- **Constraints**: Standard competitive programming limits ($0 \le N \le 10^5$).

---

## 2. Intuition & Pattern

Use a dummy node pointing to `head`. Advance `fast` pointer $N$ steps forward. Then move both `slow` and `fast` by 1 step simultaneously until `fast->next == nullptr`. At this point, `slow` will be directly at the node preceding the target node! Bypass: `slow->next = slow->next->next`.

- **Underlying Pattern**: `Two-Pointer Fixed Offset Gap ($fast - slow = N$)`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Two passes: count length $L$, then delete node at index $L - N$.

### C++17 Code
```cpp
// Two-pass approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2N) = \mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Requires traversing list twice.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard pointer manipulation below directly solves the problem.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Single pass Fixed-Gap Two Pointers with Sentinel Dummy node.

### C++17 Code
```cpp
struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

class Solution {
public:
    Node* removeNthFromEnd(Node* head, int n) {
        Node* dummy = new Node(0, head);
        Node* slow = dummy;
        Node* fast = dummy;
        
        // Advance fast pointer n steps
        for (int i = 0; i < n; i++) {
            fast = fast->next;
        }
        
        // Move both pointers until fast reaches the last node
        while (fast->next != nullptr) {
            slow = slow->next;
            fast = fast->next;
        }
        
        // slow is at node (L - n); delete target node
        Node* target = slow->next;
        slow->next = slow->next->next;
        delete target;
        
        Node* newHead = dummy->next;
        delete dummy;
        return newHead;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(L)$ single pass.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Fixed gap $N$ positions slow at the exact predecessor node in one pass.

---

## 6. Dry Run

`[1, 2, 3, 4, 5]`, $N = 2$

| Step | Action / State Change | Result |
|---|---|---|
| `Fast advance` | fast moves 2 steps from dummy to [2] | Gap = 2 |
| `Parallel move` | fast reaches [5], slow reaches [3] | slow is predecessor of [4] |
| `Delete` | slow->next = [5] (bypasses [4]) | `[1, 2, 3, 5]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Deleting the head node ($N = L$).
- Single node list ($N = 1, L = 1$ -> returns `nullptr`).

### Common Bugs to Avoid
- Not using a dummy node (deleting head requires special `if` branch without dummy).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is a dummy node essential here?**  
  **A**: If the head node needs to be deleted ($N = L$), `dummy` acts as the predecessor to `head`, allowing the exact same bypass logic `slow->next = slow->next->next` without edge cases.


---

## 9. Tags & Related Problems

- **Tags**: `LinkedList`, `Two Pointers`, `LeetCode-19`, `Medium`
- **Related problems to practice next**:
- **Delete Middle Node**: Fast-slow deletion.
