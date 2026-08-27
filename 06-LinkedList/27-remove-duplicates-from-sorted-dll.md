# Remove Duplicates from Sorted DLL (Step 6.4 — Medium Problems of DLL)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Remove Duplicates from Sorted DLL](https://takeuforward.org/data-structure/remove-duplicates-from-sorted-dll/)
- **Difficulty**: Easy
- **Statement**: Given the head of a sorted Doubly Linked List, delete all duplicate nodes such that each element appears only once in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

---

## 1. Problem, Restated

Bypass contiguous equal values in sorted DLL.

- **Input**: Head pointer of LinkedList(s) / parameters.
- **Output**: Modified LinkedList head / queried node.
- **Constraints**: Standard competitive programming limits ($0 \le N \le 10^5$).

---

## 2. Intuition & Pattern

Traverse with `curr = head`. For each node, find the next node `nextNode` whose data is strictly different: `while (nextNode && nextNode->data == curr->data) { Node* dup = nextNode; nextNode = nextNode->next; delete dup; }`. Link `curr->next = nextNode; if (nextNode) nextNode->prev = curr;`.

- **Underlying Pattern**: `Contiguous Duplicate Pointer Forward-Skipping`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Hash set of visited values in $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
// Hash set approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Ignores sorted property.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard pointer manipulation below directly solves the problem.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
In-place duplicate bypass and memory freeing in $\mathcal{O}(1)$ space.

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
    Node* removeDuplicates(Node* head) {
        Node* curr = head;
        
        while (curr != nullptr && curr->next != nullptr) {
            Node* nextNode = curr->next;
            
            // Delete all contiguous nodes with identical values
            while (nextNode != nullptr && nextNode->data == curr->data) {
                Node* duplicate = nextNode;
                nextNode = nextNode->next;
                delete duplicate; // prevent memory leak
            }
            
            // Re-link
            curr->next = nextNode;
            if (nextNode != nullptr) {
                nextNode->prev = curr;
            }
            
            curr = curr->next;
        }
        
        return head;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Bypasses all duplicate nodes in-place.

---

## 6. Dry Run

`[1] <-> [1] <-> [1] <-> [2] <-> [3] <-> [3]`

| Step | Action / State Change | Result |
|---|---|---|
| `curr = [1]` | nextNode skips two [1]s to reach [2] | [1]->next = [2], [2]->prev = [1] |
| `curr = [2]` | nextNode = [3] | Advance curr = [3] |
| `curr = [3]` | nextNode skips second [3] to reach NULL | [3]->next = NULL |
| `Result` | Duplicates removed | `[1] <-> [2] <-> [3]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- All elements identical `[1, 1, 1]` $\implies$ `[1]`.
- All unique elements.

### Common Bugs to Avoid
- Forgetting to `delete` bypassed duplicate nodes (causes memory leaks).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does this algorithm not require checking prev nodes?**  
  **A**: Because the list is sorted: all duplicate values appear in contiguous subsegments immediately following the first occurrence.


---

## 9. Tags & Related Problems

- **Tags**: `Doubly LinkedList`, `Pointers`, `Easy`
- **Related problems to practice next**:
- **Remove Duplicates from Sorted Array**: Array counterpart.
