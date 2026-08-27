# Find the Middle Node of a LinkedList (Tortoise-Hare) (Step 6.3 — Medium Problems of LL)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Find the Middle Node of a LinkedList (Tortoise-Hare)](https://takeuforward.org/data-structure/find-middle-element-in-a-linked-list/)
- **Difficulty**: Easy
- **Statement**: Given the head of a singly linked list, return the middle node of the linked list. If there are two middle nodes, return the second middle node.

---

## 1. Problem, Restated

Find the node at index $\lfloor N / 2 \rfloor$ in a single pass using Fast and Slow pointers.

- **Input**: Head of LinkedList / values to insert or delete.
- **Output**: Transformed LinkedList head / queried property.
- **Constraints**: Standard competitive programming limits ($0 \le N \le 10^5$).

---

## 2. Intuition & Pattern

Initialize `slow = head` and `fast = head`. Move `slow` by 1 step (`slow = slow->next`) and `fast` by 2 steps (`fast = fast->next->next`). When `fast` reaches the end (`fast == nullptr` for even length or `fast->next == nullptr` for odd length), `slow` will be exactly at the middle node $\lfloor N / 2 \rfloor$!

- **Underlying Pattern**: `Tortoise and Hare Fast-Slow Pointers`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Two passes: 1) Count length $N$. 2) Traverse $N/2$ steps.

### C++17 Code
```cpp
struct Node { int data; Node* next; };
Node* middleNodeTwoPass(Node* head) {
    int n = 0;
    Node* curr = head;
    while (curr) { n++; curr = curr->next; }
    curr = head;
    for (int i = 0; i < n / 2; i++) curr = curr->next;
    return curr;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N + N/2) = \mathcal{O}(N)$ time (two passes).
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Requires traversing list twice.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard pointer manipulation below directly solves the problem.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Single Pass Tortoise and Hare Fast-Slow Pointer Traversal.

### C++17 Code
```cpp
struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

class Solution {
public:
    Node* middleNode(Node* head) {
        Node* slow = head;
        Node* fast = head;
        
        // Fast moves 2 steps, Slow moves 1 step
        while (fast != nullptr && fast->next != nullptr) {
            slow = slow->next;
            fast = fast->next->next;
        }
        
        return slow; // Points to exact middle node
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N / 2) = \mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Finds middle node in a single traversal.

---

## 6. Dry Run

Odd: `[1,2,3,4,5]` vs Even: `[1,2,3,4,5,6]`

| Step | Action / State Change | Result |
|---|---|---|
| `Odd [1,2,3,4,5]` | Iter 1: s=2, f=3 -> Iter 2: s=3, f=5 (f->next=null) | Returns [3] ✅ |
| `Even [1,2,3,4,5,6]` | Iter 1: s=2, f=3 -> Iter 2: s=3, f=5 -> Iter 3: s=4, f=null | Returns [4] (second middle) ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single node ($N=1$ -> returns head).
- Two nodes ($N=2$ -> returns second node).

### Common Bugs to Avoid
- Writing `while (fast->next != nullptr && fast != nullptr)` (causes null pointer exception on `fast->next` when `fast == nullptr`). Order matters: `fast != nullptr` must come first!

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does Tortoise-Hare guarantee exact midpoint?**  
  **A**: Because `fast` travels at $2\times$ speed of `slow`. When `fast` covers distance $D = N$, `slow` has covered distance $D / 2 = N / 2$.


---

## 9. Tags & Related Problems

- **Tags**: `LinkedList`, `Two Pointers`, `Fast Slow`, `LeetCode-876`, `Easy`
- **Related problems to practice next**:
- **Detect a Loop**: Cycle detection with fast-slow.
- **Palindrome LinkedList**: Midpoint split.
