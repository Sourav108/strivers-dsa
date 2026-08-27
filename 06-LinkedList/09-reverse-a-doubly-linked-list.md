# Reverse a Doubly LinkedList (Step 6.2 — Learn Doubly LinkedList)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Reverse a Doubly LinkedList](https://takeuforward.org/data-structure/reverse-a-doubly-linked-list/)
- **Difficulty**: Medium
- **Statement**: Given the head of a Doubly Linked List, reverse the list in-place by swapping `next` and `prev` pointers for every node.

---

## 1. Problem, Restated

Invert all link directions in a DLL in a single $\mathcal{O}(N)$ pass without extra space.

- **Input**: Head of LinkedList / values to insert or delete.
- **Output**: Transformed LinkedList head / queried property.
- **Constraints**: Standard competitive programming limits ($0 \le N \le 10^5$).

---

## 2. Intuition & Pattern

Traverse each node `curr`. For every node, swap its `prev` and `next` pointers: `swap(curr->prev, curr->next)`. Move to the next node in the original sequence using `curr = curr->prev` (since pointers are now swapped!). When `curr` becomes null, the new head is the `prev` pointer of the last processed node (`last->prev`).

- **Underlying Pattern**: `In-Place Pointer Swapping (`swap(curr->next, curr->prev)`)`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Push data to stack, then overwrite node data in reverse pass in $\mathcal{O}(N)$ time and space.

### C++17 Code
```cpp
#include <stack>
using namespace std;
struct Node { int data; Node* next; Node* prev; };
Node* reverseDLLStack(Node* head) {
    stack<int> st;
    Node* curr = head;
    while (curr) { st.push(curr->data); curr = curr->next; }
    curr = head;
    while (curr) { curr->data = st.top(); st.pop(); curr = curr->next; }
    return head;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2N) = \mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ stack space.
- **Why it's not good enough**: Uses extra memory and mutates data instead of links.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard pointer manipulation below directly solves the problem.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
In-place pointer swap: `swap(curr->prev, curr->next)` in $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <algorithm>
using namespace std;

struct Node {
    int data;
    Node* next;
    Node* prev;
    Node(int val) : data(val), next(nullptr), prev(nullptr) {}
};

class Solution {
public:
    Node* reverseDLL(Node* head) {
        if (!head || !head->next) return head;
        
        Node* curr = head;
        Node* last = nullptr;
        
        while (curr != nullptr) {
            // Track last visited node to retrieve new head
            last = curr->prev;
            
            // Swap next and prev pointers
            curr->prev = curr->next;
            curr->next = last;
            
            // Advance to next node in original list (which is now curr->prev!)
            curr = curr->prev;
        }
        
        // After loop, 'last->prev' points to the new head node
        return last->prev;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Direct in-place pointer inversion without auxiliary stack.

---

## 6. Dry Run

`[10] <-> [20] <-> [30]`

| Step | Action / State Change | Result |
|---|---|---|
| `Node 10` | next: [20], prev: NULL -> Swapped: next: NULL, prev: [20] | curr moves to [20] |
| `Node 20` | next: [30], prev: [10] -> Swapped: next: [10], prev: [30] | curr moves to [30] |
| `Node 30` | next: NULL, prev: [20] -> Swapped: next: [20], prev: NULL | curr moves to NULL |
| `New Head` | last->prev = [30] | `[30] <-> [20] <-> [10]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Empty DLL (`head == nullptr`).
- Single node DLL (`head->next == nullptr`).

### Common Bugs to Avoid
- Advancing `curr = curr->next` after swap (causes infinite loop; must use `curr = curr->prev`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does curr = curr->prev advance forward?**  
  **A**: Because we just swapped `curr->next` and `curr->prev`! The original successor is now stored in `curr->prev`.


---

## 9. Tags & Related Problems

- **Tags**: `Doubly LinkedList`, `Pointers`, `In-Place`, `Medium`
- **Related problems to practice next**:
- **Reverse a LinkedList (SLL)**: Singly LL reversal.
