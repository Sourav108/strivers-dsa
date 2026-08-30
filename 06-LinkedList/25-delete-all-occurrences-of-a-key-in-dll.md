# Delete All Occurrences of a Key in DLL (Step 6.4 — Medium Problems of DLL)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Delete All Occurrences of a Key in DLL](https://takeuforward.org/data-structure/delete-all-occurrences-of-a-key-in-dll/)
- **Difficulty**: Medium
- **Statement**: Given the head of a Doubly Linked List and an integer `key`, delete all occurrences of `key` from the list and return the updated head in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

---

## 1. Problem, Restated

Traverse DLL, bypass and deallocate all nodes with `data == key`.

- **Input**: Head pointer of LinkedList(s) / parameters.
- **Output**: Modified LinkedList head / queried node.
- **Constraints**: Standard competitive programming limits ($0 \le N \le 10^5$).

---

## 2. Intuition & Pattern

Traverse with `curr = head`. If `curr->data == key`: 1) If `curr == head`, `head = curr->next`. 2) Save `nextNode = curr->next, prevNode = curr->prev`. 3) If `nextNode`, `nextNode->prev = prevNode`. 4) If `prevNode`, `prevNode->next = nextNode`. 5) `delete curr; curr = nextNode;`.

- **Underlying Pattern**: `DLL Bidirectional Node De-linking & Memory Cleanup`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Reconstruct DLL via filtered array.

### C++17 Code
```cpp
// Filtered array copy
```

### Java Code
```java
// Java equivalent
// Filtered array copy
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Extra allocation.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard pointer manipulation below directly solves the problem.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
In-place pointer bypass and deletion in $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
struct Node {
    int data;
    Node* next;
    Node* prev;
    Node(int val) : data(val), next(nullptr), prev(nullptr) {}
};

class Solution {
public:
    Node* deleteAllOccurrences(Node* head, int key) {
        Node* curr = head;
        
        while (curr != nullptr) {
            if (curr->data == key) {
                // If deleting head node
                if (curr == head) {
                    head = curr->next;
                }
                
                Node* nextNode = curr->next;
                Node* prevNode = curr->prev;
                
                if (nextNode) nextNode->prev = prevNode;
                if (prevNode) prevNode->next = nextNode;
                
                delete curr;
                curr = nextNode;
            } else {
                curr = curr->next;
            }
        }
        
        return head;
    }
};
```

### Java Code
```java
static class Node {
    int data;
    Node  next;
    Node  prev;
    public Node(int val) { /* initialized: data(val), next(null), prev(null)  */  }
};

class Solution {

    Node  deleteAllOccurrences(Node  head, int key) {
        Node  curr = head;
        
        while (curr != null) {
            if (curr.data == key) {
                // If deleting head node
                if (curr == head) {
                    head = curr.next;
                }
                
                Node  nextNode = curr.next;
                Node  prevNode = curr.prev;
                
                if (nextNode) nextNode.prev = prevNode;
                if (prevNode) prevNode.next = nextNode;
                
                delete curr;
                curr = nextNode;
            } else {
                curr = curr.next;
            }
        }
        
        return head;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Direct pointer updates and memory deallocation.

---

## 6. Dry Run

`[10] <-> [4] <-> [10] <-> [10] <-> [6]`, `key = 10`

| Step | Action / State Change | Result |
|---|---|---|
| `Delete [10] at head` | head becomes [4] | `[4] <-> [10] <-> [10] <-> [6]` |
| `Skip [4]` | curr moves to next [10] | curr = [10] |
| `Delete consecutive 10s` | prev=[4], next=[6] -> [4]->next=[6], [6]->prev=[4] | `[4] <-> [6]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- All nodes contain `key` (returns `nullptr`).
- No node contains `key`.

### Common Bugs to Avoid
- Accessing `curr->next` after `delete curr` (must cache `nextNode` before calling `delete`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why must nextNode be cached before delete curr?**  
  **A**: Calling `delete curr` deallocates the memory for `curr`. Any subsequent access `curr->next` is undefined behavior (use-after-free).


---

## 9. Tags & Related Problems

- **Tags**: `Doubly LinkedList`, `Pointers`, `Medium`
- **Related problems to practice next**:
- **Delete in DLL**: Single node deletion.
