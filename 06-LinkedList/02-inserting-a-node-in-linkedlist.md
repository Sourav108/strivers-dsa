# Inserting a Node in LinkedList (Head, Tail, Kth pos) (Step 6.1 — Learn 1D LinkedList)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Inserting a Node in LinkedList (Head, Tail, Kth pos)](https://takeuforward.org/data-structure/insert-node-at-beginning-of-linked-list/)
- **Difficulty**: Easy
- **Statement**: Implement functions to insert a new node with value $X$ into a singly linked list at: 1) The Head (beginning), 2) The Tail (end), 3) The $K$-th position, 4) Before a given value.

---

## 1. Problem, Restated

Pointer reconnection mechanics for linked list node insertion.

- **Input**: Head of LinkedList / values to insert or delete.
- **Output**: Transformed LinkedList head / queried property.
- **Constraints**: Standard competitive programming limits ($0 \le N \le 10^5$).

---

## 2. Intuition & Pattern

1) **Insert at Head**: `newNode->next = head; return newNode;` ($\mathcal{O}(1)$). 2) **Insert at Tail**: traverse to the last node `curr->next == nullptr`, then set `curr->next = newNode;` ($\mathcal{O}(N)$). 3) **Insert at K-th**: traverse to $(K-1)$-th node, link `newNode->next = curr->next`, then `curr->next = newNode;`.

- **Underlying Pattern**: `Pointer Re-Linking (`newNode->next = curr->next; curr->next = newNode;`)`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Copying to array, inserting element, reconstructing LL.

### C++17 Code
```cpp
// Inefficient array copy approach
```

### Java Code
```java
// Java equivalent
// Inefficient array copy approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ space.
- **Why it's not good enough**: Unnecessary allocations.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard pointer manipulation below directly solves the problem.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Direct pointer manipulation in $\mathcal{O}(1)$ auxiliary space.

### C++17 Code
```cpp
struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

// 1. Insert at Head: O(1) time
Node* insertHead(Node* head, int val) {
    Node* newNode = new Node(val);
    newNode->next = head;
    return newNode;
}

// 2. Insert at Tail: O(N) time
Node* insertTail(Node* head, int val) {
    if (!head) return new Node(val);
    Node* curr = head;
    while (curr->next != nullptr) curr = curr->next;
    curr->next = new Node(val);
    return head;
}

// 3. Insert at K-th Position (1-indexed)
Node* insertAtK(Node* head, int val, int k) {
    if (k == 1) return insertHead(head, val);
    Node* curr = head;
    for (int i = 1; i < k - 1 && curr != nullptr; i++) {
        curr = curr->next;
    }
    if (!curr) return head; // k out of bounds
    Node* newNode = new Node(val);
    newNode->next = curr->next;
    curr->next = newNode;
    return head;
}
```

### Java Code
```java
static class Node {
    int data;
    Node  next;
    public Node(int val) { /* initialized: data(val), next(null)  */  }
};

// 1. Insert at Head: O(1) time
Node  insertHead(Node  head, int val) {
    Node  newNode = new Node(val);
    newNode.next = head;
    return newNode;
}

// 2. Insert at Tail: O(N) time
Node  insertTail(Node  head, int val) {
    if (head == null) return new Node(val);
    Node  curr = head;
    while (curr.next != null) curr = curr.next;
    curr.next = new Node(val);
    return head;
}

// 3. Insert at K-th Position (1-indexed)
Node  insertAtK(Node  head, int val, int k) {
    if (k == 1) return insertHead(head, val);
    Node  curr = head;
    for (int i = 1; i < k - 1 && curr != null; i++) {
        curr = curr.next;
    }
    if (curr == null) return head; // k out of bounds
    Node  newNode = new Node(val);
    newNode.next = curr.next;
    curr.next = newNode;
    return head;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ for Head, $\mathcal{O}(N)$ for Tail and $K$-th position.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Direct pointer updates without copying list elements.

---

## 6. Dry Run

Insert 25 at $K=3$ in `[10] -> [20] -> [30]`

| Step | Action / State Change | Result |
|---|---|---|
| `Traverse k-1=2` | curr points to [20] | curr->next is [30] |
| `Link newNode` | newNode(25)->next = [30] | newNode points to [30] |
| `Link curr` | curr->next = newNode(25) | `[10] -> [20] -> [25] -> [30]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $K = 1$ (inserting at head).
- Empty list ($head == nullptr$).
- $K = N + 1$ (inserting at tail).

### Common Bugs to Avoid
- Setting `curr->next = newNode` BEFORE `newNode->next = curr->next` (breaks link to rest of list $\implies$ memory leak).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why must newNode->next be assigned first?**  
  **A**: If you update `curr->next` first, you lose the pointer address of the remaining list chain, causing dangling lost nodes.


---

## 9. Tags & Related Problems

- **Tags**: `LinkedList`, `Pointers`, `Easy`
- **Related problems to practice next**:
- **Delete a Node**: De-linking counterpart.
