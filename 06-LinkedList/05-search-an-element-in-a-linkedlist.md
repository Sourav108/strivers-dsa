# Search an Element in a LinkedList (Step 6.1 — Learn 1D LinkedList)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Search an Element in a LinkedList](https://takeuforward.org/data-structure/search-an-element-in-a-linked-list/)
- **Difficulty**: Easy
- **Statement**: Given the head of a singly linked list and an integer `target`, return `true` if `target` exists in the linked list, or `false` otherwise.

---

## 1. Problem, Restated

Linear search through linked list nodes checking `curr->data == target`.

- **Input**: Head of LinkedList / values to insert or delete.
- **Output**: Transformed LinkedList head / queried property.
- **Constraints**: Standard competitive programming limits ($0 \le N \le 10^5$).

---

## 2. Intuition & Pattern

Traverse from `head` node. At each node, check `if (curr->data == target) return true;`. If `nullptr` is reached without finding `target`, return `false`.

- **Underlying Pattern**: `Sequential Linear Search on Linked Chain`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Recursive search in $\mathcal{O}(N)$ time and stack space.

### C++17 Code
```cpp
struct Node { int data; Node* next; };
bool searchRecursive(Node* head, int target) {
    if (!head) return false;
    if (head->data == target) return true;
    return searchRecursive(head->next, target);
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ stack space.
- **Why it's not good enough**: Stack frame overhead.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard pointer manipulation below directly solves the problem.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Iterative Linear Search in $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

bool searchKey(Node* head, int target) {
    Node* curr = head;
    while (curr != nullptr) {
        if (curr->data == target) {
            return true; // early exit
        }
        curr = curr->next;
    }
    return false;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ worst-case, $\mathcal{O}(1)$ best-case (target at head).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Instant return upon finding target without memory allocations.

---

## 6. Dry Run

Search `target = 20` in `[10] -> [20] -> [30]`

| Step | Action / State Change | Result |
|---|---|---|
| `curr = [10]` | 10 != 20 -> curr = [20] | Advance |
| `curr = [20]` | 20 == 20 -> Target Match! | Return TRUE ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Target at head (1 comparison).
- Target at tail ($N$ comparisons).
- Target not present.

### Common Bugs to Avoid
- Dereferencing `curr->data` after `curr` is null.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Can Binary Search be applied to a sorted LinkedList in O(log N)?**  
  **A**: No! Binary search requires $\mathcal{O}(1)$ random access to midpoints (`arr[mid]`). In linked lists, accessing midpoint takes $\mathcal{O}(N)$, making binary search $\mathcal{O}(N \log N)$, which is slower than linear search $\mathcal{O}(N)$! **Skip Lists** add multi-level express pointers to achieve $\mathcal{O}(\log N)$ linked search.


---

## 9. Tags & Related Problems

- **Tags**: `LinkedList`, `Search`, `Easy`
- **Related problems to practice next**:
- **Find Length**: Traversal.
