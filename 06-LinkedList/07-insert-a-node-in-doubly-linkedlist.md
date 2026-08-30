# Insert a Node in DLL (Head, Tail, Before Kth) (Step 6.2 — Learn Doubly LinkedList)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Insert a Node in DLL (Head, Tail, Before Kth)](https://takeuforward.org/data-structure/insert-node-in-doubly-linked-list/)
- **Difficulty**: Easy
- **Statement**: Implement functions to insert a new node in a Doubly Linked List: 1) Insert before Head, 2) Insert before Tail, 3) Insert before $K$-th node, 4) Insert after a given node.

---

## 1. Problem, Restated

Maintain 4-way pointer integrity (`newNode->next`, `newNode->prev`, `curr->prev->next`, `curr->prev`) on DLL insertion.

- **Input**: Head of LinkedList / values to insert or delete.
- **Output**: Transformed LinkedList head / queried property.
- **Constraints**: Standard competitive programming limits ($0 \le N \le 10^5$).

---

## 2. Intuition & Pattern

Inserting node `N` before node `C`: 1) `N->next = C`, 2) `N->prev = C->prev`, 3) `C->prev->next = N`, 4) `C->prev = N`. Always update `N`'s pointers first before modifying existing list pointers.

- **Underlying Pattern**: `4-Way Pointer Reconnection in DLL`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Converting to array, inserting, reconstructing.

### C++17 Code
```cpp
// Array conversion
```

### Java Code
```java
// Java equivalent
// Array conversion
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Unnecessary memory.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard pointer manipulation below directly solves the problem.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
4-Way Pointer Reconnection in $\mathcal{O}(1)$ auxiliary space.

### C++17 Code
```cpp
struct Node {
    int data;
    Node* next;
    Node* prev;
    Node(int val) : data(val), next(nullptr), prev(nullptr) {}
};

// 1. Insert Before Head: O(1)
Node* insertBeforeHead(Node* head, int val) {
    Node* newHead = new Node(val, head, nullptr);
    if (head) head->prev = newHead;
    return newHead;
}

// 2. Insert Before Tail: O(N)
Node* insertBeforeTail(Node* head, int val) {
    if (!head->next) return insertBeforeHead(head, val);
    Node* tail = head;
    while (tail->next != nullptr) tail = tail->next;
    
    Node* prevNode = tail->prev;
    Node* newNode = new Node(val, tail, prevNode);
    prevNode->next = newNode;
    tail->prev = newNode;
    return head;
}

// 3. Insert Before K-th Node (1-indexed)
Node* insertBeforeK(Node* head, int val, int k) {
    if (k == 1) return insertBeforeHead(head, val);
    Node* curr = head;
    for (int i = 1; i < k && curr != nullptr; i++) curr = curr->next;
    if (!curr) return head;
    
    Node* prevNode = curr->prev;
    Node* newNode = new Node(val, curr, prevNode);
    prevNode->next = newNode;
    curr->prev = newNode;
    return head;
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

// 1. Insert Before Head: O(1)
Node  insertBeforeHead(Node  head, int val) {
    Node  newHead = new Node(val, head, null);
    if (head) head.prev = newHead;
    return newHead;
}

// 2. Insert Before Tail: O(N)
Node  insertBeforeTail(Node  head, int val) {
    if (head.next == null) return insertBeforeHead(head, val);
    Node  tail = head;
    while (tail.next != null) tail = tail.next;
    
    Node  prevNode = tail.prev;
    Node  newNode = new Node(val, tail, prevNode);
    prevNode.next = newNode;
    tail.prev = newNode;
    return head;
}

// 3. Insert Before K-th Node (1-indexed)
Node  insertBeforeK(Node  head, int val, int k) {
    if (k == 1) return insertBeforeHead(head, val);
    Node  curr = head;
    for (int i = 1; i < k && curr != null; i++) curr = curr.next;
    if (curr == null) return head;
    
    Node  prevNode = curr.prev;
    Node  newNode = new Node(val, curr, prevNode);
    prevNode.next = newNode;
    curr.prev = newNode;
    return head;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ for Head, $\mathcal{O}(N)$ for Tail/$K$-th node.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Direct 4-way pointer updates in $\mathcal{O}(1)$ operations.

---

## 6. Dry Run

Insert 15 before [20] in `[10] <-> [20] <-> [30]`

| Step | Action / State Change | Result |
|---|---|---|
| `curr = [20]` | prevNode = [10] | Found target |
| `newNode(15)` | next = [20], prev = [10] | newNode configured |
| `Re-link` | [10]->next = [15], [20]->prev = [15] | `[10] <-> [15] <-> [20] <-> [30]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Inserting before Head ($K=1$).
- Inserting in single-node list.

### Common Bugs to Avoid
- Forgetting to update `prevNode->next` or `curr->prev` (breaks bidirectional integrity).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why are 4 pointer updates required in DLL instead of 2 in SLL?**  
  **A**: In SLL, each link is 1-way (`A->B`). In DLL, each link is 2-way (`A->B` and `B<-A`), requiring 2 incoming and 2 outgoing updates.


---

## 9. Tags & Related Problems

- **Tags**: `Doubly LinkedList`, `Pointers`, `Easy`
- **Related problems to practice next**:
- **Delete in DLL**: DLL deletion.
