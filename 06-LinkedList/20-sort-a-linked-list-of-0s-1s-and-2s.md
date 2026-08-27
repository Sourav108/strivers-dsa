# Sort a LinkedList of 0s, 1s and 2s by Changing Links (Step 6.3 — Medium Problems of LL)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Sort a LinkedList of 0s, 1s and 2s by Changing Links](https://takeuforward.org/data-structure/sort-a-linked-list-of-0s-1s-and-2s-by-changing-links/)
- **Difficulty**: Medium
- **Statement**: Given a linked list of 0s, 1s and 2s, sort the list by modifying the node links (not by overwriting node data).

---

## 1. Problem, Restated

Partition nodes into 3 separate linked lists for 0s, 1s, and 2s, then stitch them together in $\mathcal{O}(N)$ time.

- **Input**: Head pointer of LinkedList(s) / parameters.
- **Output**: Modified LinkedList head / queried node.
- **Constraints**: Standard competitive programming limits ($0 \le N \le 10^5$).

---

## 2. Intuition & Pattern

Create 3 dummy heads: `zeroHead`, `oneHead`, `twoHead`. Traverse input list: if `curr->data == 0`, append to `zeroTail`; if 1, append to `oneTail`; if 2, append to `twoTail`. Connect `zeroTail->next = (oneHead->next) ? oneHead->next : twoHead->next`, and `oneTail->next = twoHead->next`. Set `twoTail->next = nullptr`.

- **Underlying Pattern**: `3 Dummy Head Partitioning & Link Stitching`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Counting 0s, 1s, 2s and overwriting node values.

### C++17 Code
```cpp
// Overwriting data approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Violates problem constraint of modifying node links.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard pointer manipulation below directly solves the problem.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
3-Way Dummy Head Linking in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

class Solution {
public:
    Node* segregate(Node *head) {
        if (!head || !head->next) return head;
        
        // 3 Dummy heads
        Node zeroDummy(0), oneDummy(0), twoDummy(0);
        Node* zero = &zeroDummy;
        Node* one = &oneDummy;
        Node* two = &twoDummy;
        
        Node* curr = head;
        while (curr != nullptr) {
            if (curr->data == 0) {
                zero->next = curr;
                zero = zero->next;
            } else if (curr->data == 1) {
                one->next = curr;
                one = one->next;
            } else {
                two->next = curr;
                two = two->next;
            }
            curr = curr->next;
        }
        
        // Stitch lists: 0s -> 1s -> 2s
        zero->next = (oneDummy.next) ? oneDummy.next : twoDummy.next;
        one->next = twoDummy.next;
        two->next = nullptr; // terminate list
        
        return zeroDummy.next;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Re-links existing nodes without allocating new nodes or mutating data values.

---

## 6. Dry Run

`[1, 2, 0, 2, 1, 0]`

| Step | Action / State Change | Result |
|---|---|---|
| `Partitioning` | 0s: `[0, 0]`, 1s: `[1, 1]`, 2s: `[2, 2]` | Built via 3 pointers |
| `Stitch` | [0, 0]->next = [1, 1], [1, 1]->next = [2, 2] | `[0, 0, 1, 1, 2, 2] -> NULL` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- List with no 1s (connects 0s directly to 2s).
- List with only 0s.

### Common Bugs to Avoid
- Forgetting `two->next = nullptr` (creates cyclic loop at tail).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How to handle case where no 1s exist in list?**  
  **A**: The ternary check `zero->next = (oneDummy.next) ? oneDummy.next : twoDummy.next` safely connects 0s directly to 2s if no 1s exist!


---

## 9. Tags & Related Problems

- **Tags**: `LinkedList`, `Pointers`, `In-Place`, `Medium`
- **Related problems to practice next**:
- **Dutch National Flag (Arrays)**: Array counterpart.
