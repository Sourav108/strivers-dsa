# Find the Length of a LinkedList (Step 6.1 — Learn 1D LinkedList)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Find the Length of a LinkedList](https://takeuforward.org/data-structure/find-the-length-of-a-linked-list/)
- **Difficulty**: Easy
- **Statement**: Given the head of a singly linked list, find and return the total number of nodes (length) in the list.

---

## 1. Problem, Restated

Traverse the linked list counting nodes until reaching `nullptr`.

- **Input**: Head of LinkedList / values to insert or delete.
- **Output**: Transformed LinkedList head / queried property.
- **Constraints**: Standard competitive programming limits ($0 \le N \le 10^5$).

---

## 2. Intuition & Pattern

Initialize `count = 0, curr = head`. While `curr != nullptr`, increment `count++` and advance `curr = curr->next`. Return `count` in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

- **Underlying Pattern**: `Sequential Pointer Traversal`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Recursive length counting using $\mathcal{O}(N)$ call stack frames.

### C++17 Code
```cpp
struct Node { int data; Node* next; };
int lengthRecursive(Node* head) {
    if (!head) return 0;
    return 1 + lengthRecursive(head->next);
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ stack memory.
- **Why it's not good enough**: Recursion uses stack frames.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard pointer manipulation below directly solves the problem.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Iterative pointer traversal in $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

int getLength(Node* head) {
    int count = 0;
    Node* curr = head;
    while (curr != nullptr) {
        count++;
        curr = curr->next;
    }
    return count;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Iterative loop avoids call stack overhead.

---

## 6. Dry Run

List: `[10] -> [20] -> [30] -> NULL`

| Step | Action / State Change | Result |
|---|---|---|
| `curr = [10]` | count = 1, curr = [20] | In loop |
| `curr = [20]` | count = 2, curr = [30] | In loop |
| `curr = [30]` | count = 3, curr = NULL | In loop |
| `curr == NULL` | Loop ends | Return count = 3 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Empty list `head == nullptr` (returns 0).
- Single node list (returns 1).

### Common Bugs to Avoid
- Writing `while (curr->next != nullptr)` which undercounts by 1 (stops on last node instead of processing it).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is finding length of array O(1) while LL is O(N)?**  
  **A**: Arrays store length metadata or have contiguous pointer arithmetic `(end - begin)`. Standard linked lists only know the head pointer, requiring traversal to find the tail.


---

## 9. Tags & Related Problems

- **Tags**: `LinkedList`, `Traversal`, `Easy`
- **Related problems to practice next**:
- **Search an Element**: Element search.
