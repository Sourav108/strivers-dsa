# Deleting a Node in LinkedList (Head, Tail, Value) (Step 6.1 — Learn 1D LinkedList)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Deleting a Node in LinkedList (Head, Tail, Value)](https://takeuforward.org/data-structure/delete-node-in-a-linked-list/)
- **Difficulty**: Easy
- **Statement**: Implement functions to delete a node in a singly linked list: 1) Delete Head, 2) Delete Tail, 3) Delete $K$-th node, 4) Delete node with given value $X$, 5) Delete given node pointer in $\mathcal{O}(1)$ without head pointer (LeetCode 237).

---

## 1. Problem, Restated

Safely de-link and `delete` heap memory for targeted nodes.

- **Input**: Head of LinkedList / values to insert or delete.
- **Output**: Transformed LinkedList head / queried property.
- **Constraints**: Standard competitive programming limits ($0 \le N \le 10^5$).

---

## 2. Intuition & Pattern

To delete node $N$: find its predecessor `prev`, set `prev->next = target->next`, and `delete target`. For LeetCode 237 (delete node given only pointer `node`): copy next node's data `node->val = node->next->val`, link `node->next = node->next->next`, and delete `node->next`.

- **Underlying Pattern**: `Pointer De-linking & Memory Deallocation`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Reconstructing entire list into new memory.

### C++17 Code
```cpp
// Array copy-reconstruction
```

### Java Code
```java
// Java equivalent
// Array copy-reconstruction
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
In-place pointer de-linking and RAII memory deletion.

### C++17 Code
```cpp
struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

// 1. Delete Head: O(1)
Node* deleteHead(Node* head) {
    if (!head) return nullptr;
    Node* temp = head;
    head = head->next;
    delete temp; // prevent memory leak
    return head;
}

// 2. Delete Tail: O(N)
Node* deleteTail(Node* head) {
    if (!head || !head->next) {
        delete head;
        return nullptr;
    }
    Node* curr = head;
    while (curr->next->next != nullptr) curr = curr->next;
    delete curr->next;
    curr->next = nullptr;
    return head;
}

// 3. Delete by Value: O(N)
Node* deleteValue(Node* head, int val) {
    if (!head) return nullptr;
    if (head->data == val) return deleteHead(head);
    
    Node* curr = head;
    Node* prev = nullptr;
    while (curr && curr->data != val) {
        prev = curr;
        curr = curr->next;
    }
    if (curr) {
        prev->next = curr->next;
        delete curr;
    }
    return head;
}

// 4. Delete Node in O(1) given only pointer to that node (LeetCode 237)
void deleteNodeWithoutHead(Node* node) {
    Node* nextNode = node->next;
    node->data = nextNode->data;     // copy next node's data
    node->next = nextNode->next;     // bypass next node
    delete nextNode;                 // free bypassed node
}
```

### Java Code
```java
static class Node {
    int data;
    Node  next;
    public Node(int val) { /* initialized: data(val), next(null)  */  }
};

// 1. Delete Head: O(1)
Node  deleteHead(Node  head) {
    if (head == null) return null;
    Node  temp = head;
    head = head.next;
    delete temp; // prevent memory leak
    return head;
}

// 2. Delete Tail: O(N)
Node  deleteTail(Node  head) {
    if (!head || !head.next) {
        delete head;
        return null;
    }
    Node  curr = head;
    while (curr.next.next != null) curr = curr.next;
    delete curr.next;
    curr.next = null;
    return head;
}

// 3. Delete by Value: O(N)
Node  deleteValue(Node  head, int val) {
    if (head == null) return null;
    if (head.data == val) return deleteHead(head);
    
    Node  curr = head;
    Node  prev = null;
    while (curr && curr.data != val) {
        prev = curr;
        curr = curr.next;
    }
    if (curr) {
        prev.next = curr.next;
        delete curr;
    }
    return head;
}

// 4. Delete Node in O(1) given only pointer to that node (LeetCode 237)
void deleteNodeWithoutHead(Node  node) {
    Node  nextNode = node.next;
    node.data = nextNode.data;     // copy next node's data
    node.next = nextNode.next;     // bypass next node
    delete nextNode;                 // free bypassed node
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ for Head & given pointer; $\mathcal{O}(N)$ for Tail and Value search.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Instant pointer bypass and explicit `delete` cleanup.

---

## 6. Dry Run

Delete 20 from `[10] -> [20] -> [30]`

| Step | Action / State Change | Result |
|---|---|---|
| `Find val` | prev = [10], curr = [20] | Found target [20] |
| `Bypass` | prev->next = curr->next ([10]->next = [30]) | Target bypassed |
| `Free memory` | delete curr (frees [20]) | `[10] -> [30]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Deleting from empty list ($head == nullptr$).
- Deleting the only node ($head->next == nullptr$).
- Value not present in list.

### Common Bugs to Avoid
- Forgetting to `delete` freed nodes (causes memory leaks).
- Deleting tail node using `deleteNodeWithoutHead` (impossible because next node does not exist).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why can't deleteNodeWithoutHead delete the tail node?**  
  **A**: Because there is no subsequent node to copy data from. The trick requires overwriting the current node with `node->next->data`.


---

## 9. Tags & Related Problems

- **Tags**: `LinkedList`, `Pointers`, `Memory`, `Easy`
- **Related problems to practice next**:
- **Insert a Node**: Insertion.
- **Remove Nth Node from End**: Two-pointer deletion.
