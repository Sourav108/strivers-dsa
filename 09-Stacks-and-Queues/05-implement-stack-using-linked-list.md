# Implement Stack using LinkedList (Step 9.1 — Learning)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Implement Stack using LinkedList](https://takeuforward.org/data-structure/implement-stack-using-linked-list/)
- **Difficulty**: Easy
- **Statement**: Implement a Stack using a singly linked list where `push`, `pop`, `top`, and `isEmpty` execute in $\mathcal{O}(1)$ time without fixed size capacity limits.

---

## 1. Problem, Restated

Use linked list head insertion and deletion as stack push and pop.

- **Input**: Class methods / expression string.
- **Output**: Operation returns / transformed expression.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Insert and delete at the HEAD of the linked list. `push(x)`: `head = new Node(x, head)`. `pop()`: `temp = head; head = head->next; delete temp;`. `top()`: `head->data`. Since head operations on a linked list are strictly $\mathcal{O}(1)$, the stack requires zero resizing overhead.

- **Underlying Pattern**: `Head-Node LIFO Pointer Manipulation`.

---

## 3. Approach 1 — Naive / Basic Attempt

### Idea
Inserting and deleting at TAIL of SLL in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
// Tail insertion SLL O(N)
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ per operation.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Traverses to tail.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic data structure implementation below directly provides optimal bounds.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
Head-Node Stack Insertion in $\mathcal{O}(1)$ time.

### C++17 Code
```cpp
struct Node {
    int data;
    Node* next;
    Node(int val, Node* nextNode = nullptr) : data(val), next(nextNode) {}
};

class LinkedListStack {
private:
    Node* head;
    int currentSize;

public:
    LinkedListStack() : head(nullptr), currentSize(0) {}
    
    ~LinkedListStack() {
        while (head != nullptr) {
            Node* temp = head;
            head = head->next;
            delete temp;
        }
    }
    
    void push(int x) {
        head = new Node(x, head);
        currentSize++;
    }
    
    int pop() {
        if (isEmpty()) return -1;
        Node* temp = head;
        int val = temp->data;
        head = head->next;
        delete temp;
        currentSize--;
        return val;
    }
    
    int top() {
        if (isEmpty()) return -1;
        return head->data;
    }
    
    int size() {
        return currentSize;
    }
    
    bool isEmpty() {
        return head == nullptr;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ strict time for all operations.
- **Space Complexity**: $\mathcal{O}(N)$ heap memory (16 bytes per node).
- **Why this is optimal**: Dynamic heap allocation eliminates stack overflow capacity limits.

---

## 6. Dry Run

`push(10), push(20), top(), pop()`

| Step | Action / State Change | Result |
|---|---|---|
| `push(10)` | head -> [10|null] | size = 1 |
| `push(20)` | head -> [20] -> [10|null] | size = 2 |
| `top()` | returns head->data | 20 |
| `pop()` | frees [20], head -> [10|null] | returns 20 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Pop on empty stack (returns -1).
- Memory cleanup in destructor.

### Common Bugs to Avoid
- Forgetting to `delete temp` in `pop()` (creates memory leaks).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is the memory trade-off of LL Stack vs Array Stack?**  
  **A**: LL stack avoids fixed capacity limits but uses 16 bytes per element (8 bytes data + padding + 8 bytes pointer) with memory fragmentation, whereas Array Stack uses 4 bytes per element in contiguous cache-friendly memory.


---

## 9. Tags & Related Problems

- **Tags**: `Stack`, `LinkedList`, `Data Structures`, `Easy`
- **Related problems to practice next**:
- **Implement Queue using LinkedList**: Queue counterpart.
