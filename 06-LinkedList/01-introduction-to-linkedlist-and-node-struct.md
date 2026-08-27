# Introduction to LinkedList & Node Struct (Step 6.1 — Learn 1D LinkedList)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Introduction to LinkedList & Node Struct](https://takeuforward.org/data-structure/introduction-to-linked-list/)
- **Difficulty**: Easy
- **Statement**: Define a standard singly linked list `Node` struct in C++ with constructors, allocate nodes on heap using `new`, convert a dynamic vector into a linked list, and traverse it.

---

## 1. Problem, Restated

Understand non-contiguous heap node memory linked via 8-byte pointer references.

- **Input**: Head of LinkedList / values to insert or delete.
- **Output**: Transformed LinkedList head / queried property.
- **Constraints**: Standard competitive programming limits ($0 \le N \le 10^5$).

---

## 2. Intuition & Pattern

Unlike arrays where elements are stored contiguously in memory, a Linked List stores each element in an independently allocated heap `Node` struct containing data and a `next` pointer. Converting a vector to a linked list creates the head node, then iterates maintaining a `tail` pointer to append subsequent nodes in $\mathcal{O}(1)$ per element.

- **Underlying Pattern**: `Heap Node Allocation & Dynamic Pointer Traversal`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Allocating nodes without constructor or memory cleanup.

### C++17 Code
```cpp
struct Node { int data; Node* next; };
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Lacks constructor initializers.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard pointer manipulation below directly solves the problem.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Idiomatic C++ Node struct with explicit constructors and vector conversion.

### C++17 Code
```cpp
#include <iostream>
#include <vector>
using namespace std;

struct Node {
    int data;
    Node* next;
    
    Node(int val) : data(val), next(nullptr) {}
    Node(int val, Node* nextNode) : data(val), next(nextNode) {}
};

// Convert vector to Singly Linked List
Node* constructLL(const vector<int>& arr) {
    if (arr.empty()) return nullptr;
    
    Node* head = new Node(arr[0]);
    Node* tail = head;
    
    for (size_t i = 1; i < arr.size(); i++) {
        tail->next = new Node(arr[i]);
        tail = tail->next;
    }
    
    return head;
}

void printLL(Node* head) {
    Node* curr = head;
    while (curr != nullptr) {
        cout << curr->data << " -> ";
        curr = curr->next;
    }
    cout << "NULL\n";
}

int main() {
    vector<int> v = {10, 20, 30, 40};
    Node* head = constructLL(v);
    printLL(head);
    return 0;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time to construct and traverse.
- **Space Complexity**: $\mathcal{O}(N)$ heap memory.
- **Why this is optimal**: Tail pointer allows $\mathcal{O}(1)$ append per node.

---

## 6. Dry Run

Vector `[10, 20, 30]` to LL

| Step | Action / State Change | Result |
|---|---|---|
| `Head` | new Node(10) | head -> [10|null], tail = head |
| `i = 1` | tail->next = new Node(20) | [10] -> [20|null], tail = [20] |
| `i = 2` | tail->next = new Node(30) | [10] -> [20] -> [30|null] ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Empty vector (returns `nullptr`).
- Single element vector.

### Common Bugs to Avoid
- Dangling pointer access (`curr->next` when `curr == nullptr`).
- Memory leaks (forgetting `delete`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why use struct over class for Node in C++?**  
  **A**: In C++, `struct` defaults to public member visibility, making `node->data` and `node->next` accessible without boilerplate getters/setters.


---

## 9. Tags & Related Problems

- **Tags**: `LinkedList`, `Data Structures`, `Easy`
- **Related problems to practice next**:
- **Insert a Node**: Insertion operations.
