# Delete a Node in DLL (Head, Tail, Target) (Step 6.2 — Learn Doubly LinkedList)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Delete a Node in DLL (Head, Tail, Target)](https://takeuforward.org/data-structure/delete-node-in-doubly-linked-list/)
- **Difficulty**: Easy
- **Statement**: Implement functions to delete a node in a Doubly Linked List: 1) Delete Head, 2) Delete Tail, 3) Delete $K$-th node, 4) Delete a given node pointer in $\mathcal{O}(1)$ time without head.

---

## 1. Problem, Restated

Bypass and delete nodes in a DLL handling edge cases for head and tail.

- **Input**: Head of LinkedList / values to insert or delete.
- **Output**: Transformed LinkedList head / queried property.
- **Constraints**: Standard competitive programming limits ($0 \le N \le 10^5$).

---

## 2. Intuition & Pattern

To delete target node `curr`: 1) `curr->prev->next = curr->next`, 2) `curr->next->prev = curr->prev`, 3) `delete curr`. If `curr` is the head, update `head = curr->next; head->prev = nullptr;`.

- **Underlying Pattern**: `DLL Node Bypass (`prev->next = next; next->prev = prev;`)`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Reconstruction via array.

### C++17 Code
```cpp
// Array copy
```

### Java Code
```java
// Java equivalent
// Array copy
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Memory churn.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard pointer manipulation below directly solves the problem.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Direct pointer bypass and `delete` in $\mathcal{O}(1)$ auxiliary space.

### C++17 Code
```cpp
struct Node {
    int data;
    Node* next;
    Node* prev;
    Node(int val) : data(val), next(nullptr), prev(nullptr) {}
};

// 1. Delete Head: O(1)
Node* deleteHeadDLL(Node* head) {
    if (!head || !head->next) { delete head; return nullptr; }
    Node* nextNode = head->next;
    nextNode->prev = nullptr;
    head->next = nullptr;
    delete head;
    return nextNode;
}

// 2. Delete Tail: O(N)
Node* deleteTailDLL(Node* head) {
    if (!head || !head->next) { delete head; return nullptr; }
    Node* tail = head;
    while (tail->next != nullptr) tail = tail->next;
    Node* prevNode = tail->prev;
    prevNode->next = nullptr;
    tail->prev = nullptr;
    delete tail;
    return head;
}

// 3. Delete given node in O(1) time without head pointer
void deleteNodeDLL(Node* node) {
    Node* prevNode = node->prev;
    Node* nextNode = node->next;
    
    if (prevNode) prevNode->next = nextNode;
    if (nextNode) nextNode->prev = prevNode;
    
    node->next = nullptr;
    node->prev = nullptr;
    delete node;
}
```

### Java Code
```java
static class Node {
    int data;
    Node  next;
    Node  prev;
    public Node(int val) { /* initialized: data(val), next(null), prev(null)  */  }
};

// 1. Delete Head: O(1)
Node  deleteHeadDLL(Node  head) {
    if (!head || !head.next) { delete head; return null; }
    Node  nextNode = head.next;
    nextNode.prev = null;
    head.next = null;
    delete head;
    return nextNode;
}

// 2. Delete Tail: O(N)
Node  deleteTailDLL(Node  head) {
    if (!head || !head.next) { delete head; return null; }
    Node  tail = head;
    while (tail.next != null) tail = tail.next;
    Node  prevNode = tail.prev;
    prevNode.next = null;
    tail.prev = null;
    delete tail;
    return head;
}

// 3. Delete given node in O(1) time without head pointer
void deleteNodeDLL(Node  node) {
    Node  prevNode = node.prev;
    Node  nextNode = node.next;
    
    if (prevNode) prevNode.next = nextNode;
    if (nextNode) nextNode.prev = prevNode;
    
    node.next = null;
    node.prev = null;
    delete node;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ for Head & given pointer; $\mathcal{O}(N)$ for Tail.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: DLL enables true $\mathcal{O}(1)$ node deletion anywhere without searching for predecessor.

---

## 6. Dry Run

Delete node [20] in `[10] <-> [20] <-> [30]`

| Step | Action / State Change | Result |
|---|---|---|
| `prev = [10], next = [30]` | [10]->next = [30] | Forward link bypassed |
| `next = [30]` | [30]->prev = [10] | Backward link bypassed |
| `delete [20]` | Memory freed | `[10] <-> [30]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single node DLL ($head->next == nullptr$).
- Deleting head node ($node->prev == nullptr$).
- Deleting tail node ($node->next == nullptr$).

### Common Bugs to Avoid
- Null pointer dereference when accessing `node->next->prev` if `node->next` is null.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why can DLL delete any given node in O(1) while SLL cannot?**  
  **A**: Because DLL nodes hold `prev` pointers, giving immediate access to the predecessor node without scanning from the head!


---

## 9. Tags & Related Problems

- **Tags**: `Doubly LinkedList`, `Pointers`, `Easy`
- **Related problems to practice next**:
- **LRU Cache**: Uses O(1) DLL deletion.
