# Introduction to Doubly LinkedList & Structure (Step 6.2 — Learn Doubly LinkedList)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Introduction to Doubly LinkedList & Structure](https://takeuforward.org/data-structure/introduction-to-doubly-linked-list/)
- **Difficulty**: Easy
- **Statement**: Define a Doubly Linked List `Node` struct in C++ with `data`, `next`, and `prev` pointers, convert an array to a DLL, and implement bidirectional traversal.

---

## 1. Problem, Restated

Construct a two-way linked list enabling $\mathcal{O}(1)$ forward and backward navigation.

- **Input**: Head of LinkedList / values to insert or delete.
- **Output**: Transformed LinkedList head / queried property.
- **Constraints**: Standard competitive programming limits ($0 \le N \le 10^5$).

---

## 2. Intuition & Pattern

Each node in a Doubly Linked List holds two 8-byte pointer references: `next` (points to successor) and `prev` (points to predecessor). This enables $\mathcal{O}(1)$ deletion and bidirectional navigation at the cost of 8 additional bytes per node.

- **Underlying Pattern**: `Bidirectional Pointer Linking (`node->next` and `node->prev`)`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Singly linked list without back-pointers.

### C++17 Code
```cpp
// SLL lacking prev pointers
```

### Java Code
```java
// Java equivalent
// SLL lacking prev pointers
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Lacks bidirectional capability.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard pointer manipulation below directly solves the problem.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Doubly Linked Node struct with vector constructor.

### C++17 Code
```cpp
#include <iostream>
#include <vector>
using namespace std;

struct Node {
    int data;
    Node* next;
    Node* prev;
    
    Node(int val) : data(val), next(nullptr), prev(nullptr) {}
    Node(int val, Node* nextN, Node* prevN) : data(val), next(nextN), prev(prevN) {}
};

// Convert array to Doubly Linked List
Node* constructDLL(const vector<int>& arr) {
    if (arr.empty()) return nullptr;
    
    Node* head = new Node(arr[0]);
    Node* prevNode = head;
    
    for (size_t i = 1; i < arr.size(); i++) {
        Node* temp = new Node(arr[i], nullptr, prevNode);
        prevNode->next = temp;
        prevNode = temp;
    }
    
    return head;
}

void printForwardAndBackward(Node* head) {
    Node* curr = head;
    Node* tail = nullptr;
    cout << "Forward: ";
    while (curr) {
        cout << curr->data << " <-> ";
        tail = curr;
        curr = curr->next;
    }
    cout << "NULL\nBackward: ";
    while (tail) {
        cout << tail->data << " <-> ";
        tail = tail->prev;
    }
    cout << "NULL\n";
}

int main() {
    vector<int> arr = {1, 2, 3, 4};
    Node* head = constructDLL(arr);
    printForwardAndBackward(head);
    return 0;
}
```

### Java Code
```java
import java.util.*;

static class Node {
    int data;
    Node  next;
    Node  prev;
    
    public Node(int val) { /* initialized: data(val), next(null), prev(null)  */  }
    public Node(int val, Node  nextN, Node  prevN) { /* initialized: data(val), next(nextN), prev(prevN)  */  }
};

// Convert array to Doubly Linked List
Node  constructDLL(int[] arr) {
    if (arr.isEmpty()) return null;
    
    Node  head = new Node(arr[0]);
    Node  prevNode = head;
    
    for (int i = 1; i < arr.length; i++) {
        Node  temp = new Node(arr[i], null, prevNode);
        prevNode.next = temp;
        prevNode = temp;
    }
    
    return head;
}

void printForwardAndBackward(Node  head) {
    Node  curr = head;
    Node  tail = null;
    System.out.print("Forward: ");
    while (curr) {
        System.out.print(curr.data << " <. ");
        tail = curr;
        curr = curr.next;
    }
    System.out.print("null\nBackward: ");
    while (tail) {
        System.out.print(tail.data << " <. ");
        tail = tail.prev;
    }
    System.out.print("null\n");
}

int main() {
    int[] arr = {1, 2, 3, 4};
    Node  head = constructDLL(arr);
    printForwardAndBackward(head);
    return 0;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ heap memory (24 bytes per node on 64-bit architecture: 4 data + 4 pad + 8 next + 8 prev).
- **Why this is optimal**: Constructs bidirectional links in a single pass.

---

## 6. Dry Run

Vector `[1, 2, 3]` to DLL

| Step | Action / State Change | Result |
|---|---|---|
| `Node 1` | head = [null|1|null] | prevNode = [1] |
| `Node 2` | temp = [1|2|null], prevNode->next = [2] | `[1] <-> [2]` |
| `Node 3` | temp = [2|3|null], prevNode->next = [3] | `[1] <-> [2] <-> [3]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Empty array (returns `nullptr`).
- Single element array (`head->next == nullptr` and `head->prev == nullptr`).

### Common Bugs to Avoid
- Forgetting to link `temp->prev = prevNode` (leaves DLL broken in reverse).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What are the trade-offs of DLL over SLL?**  
  **A**: Pros: $\mathcal{O}(1)$ deletion given node pointer, bidirectional traversal, easy tail operations. Cons: Extra 8 bytes per node for `prev` pointer, more pointer manipulation on inserts/deletes.


---

## 9. Tags & Related Problems

- **Tags**: `Doubly LinkedList`, `Pointers`, `Easy`
- **Related problems to practice next**:
- **Insert in DLL**: DLL insertion.
- **Reverse a DLL**: DLL reversal.
