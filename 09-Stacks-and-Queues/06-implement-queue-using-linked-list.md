# Implement Queue using LinkedList (Step 9.1 — Learning)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Implement Queue using LinkedList](https://takeuforward.org/data-structure/implement-queue-using-linked-list/)
- **Difficulty**: Easy
- **Statement**: Implement a Queue using a singly linked list maintaining `front` and `rear` pointers such that `push`, `pop`, `peek`, and `isEmpty` run in $\mathcal{O}(1)$ time.

---

## 1. Problem, Restated

Maintain `front` (for $\mathcal{O}(1)$ pop) and `rear` (for $\mathcal{O}(1)$ push) pointers.

- **Input**: Class methods / expression string.
- **Output**: Operation returns / transformed expression.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Maintain `frontNode` and `rearNode`. `push(x)`: append at `rearNode->next` and advance `rearNode`. `pop()`: remove from `frontNode` and advance `frontNode`. When queue becomes empty after pop, set `rearNode = nullptr`. Both operations run in $\mathcal{O}(1)$ time.

- **Underlying Pattern**: `Front-Pop and Rear-Push Dual Pointer SLL`.

---

## 3. Approach 1 — Naive / Basic Attempt

### Idea
SLL with single head pointer traversing to tail on push in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
// Single head SLL queue O(N) push
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ per push.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Traverses to tail.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic data structure implementation below directly provides optimal bounds.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
Dual-Pointer (Front + Rear) Linked Queue in $\mathcal{O}(1)$ time.

### C++17 Code
```cpp
struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

class LinkedListQueue {
private:
    Node* frontNode;
    Node* rearNode;
    int currentSize;

public:
    LinkedListQueue() : frontNode(nullptr), rearNode(nullptr), currentSize(0) {}
    
    ~LinkedListQueue() {
        while (frontNode != nullptr) {
            Node* temp = frontNode;
            frontNode = frontNode->next;
            delete temp;
        }
    }
    
    void push(int x) {
        Node* newNode = new Node(x);
        if (isEmpty()) {
            frontNode = rearNode = newNode;
        } else {
            rearNode->next = newNode;
            rearNode = newNode;
        }
        currentSize++;
    }
    
    int pop() {
        if (isEmpty()) return -1;
        Node* temp = frontNode;
        int val = temp->data;
        frontNode = frontNode->next;
        if (frontNode == nullptr) rearNode = nullptr; // queue became empty
        delete temp;
        currentSize--;
        return val;
    }
    
    int peek() {
        if (isEmpty()) return -1;
        return frontNode->data;
    }
    
    int size() {
        return currentSize;
    }
    
    bool isEmpty() {
        return frontNode == nullptr;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ strict time for all operations.
- **Space Complexity**: $\mathcal{O}(N)$ heap memory.
- **Why this is optimal**: Front and rear pointers enable $\mathcal{O}(1)$ access to both ends of the list.

---

## 6. Dry Run

`push(1), push(2), pop(), pop()`

| Step | Action / State Change | Result |
|---|---|---|
| `push(1)` | front = rear = [1] | size = 1 |
| `push(2)` | rear->next = [2], rear = [2] | `[1] -> [2]` |
| `pop()` | frees [1], front = [2] | returns 1 |
| `pop()` | frees [2], front = rear = null | returns 2 (Empty ✅) |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Popping the only element in queue (must set `rear = nullptr`).
- Pop on empty queue.

### Common Bugs to Avoid
- Dangling `rear` pointer when `front` becomes null after deleting the last node.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why must rear be set to nullptr when front becomes nullptr?**  
  **A**: If the last node is deleted, `rear` still points to the deallocated memory. Setting `rear = nullptr` avoids use-after-free bugs on subsequent pushes.


---

## 9. Tags & Related Problems

- **Tags**: `Queue`, `LinkedList`, `Data Structures`, `Easy`
- **Related problems to practice next**:
- **Implement Stack using LinkedList**: Stack counterpart.
