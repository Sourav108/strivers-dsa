# Implement Queue using Arrays (Step 9.1 — Learning)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Implement Queue using Arrays](https://takeuforward.org/data-structure/implement-queue-using-array/)
- **Difficulty**: Easy
- **Statement**: Design a First-In-First-Out (FIFO) Circular Queue using a fixed-size array in C++ supporting `push(x)`, `pop()`, `front()`, `size()`, and `isEmpty()` in $\mathcal{O}(1)$ time per operation.

---

## 1. Problem, Restated

Implement circular ring buffer queue using modulo index arithmetic.

- **Input**: Class methods / expression string.
- **Output**: Operation returns / transformed expression.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

A linear array wastes space as `front` advances rightward. A **Circular Queue** wraps pointers around using modulo arithmetic: `rear = (rear + 1) % capacity` on push, and `front = (front + 1) % capacity` on pop. Tracking `currentSize` allows disambiguating between full and empty queue in $\mathcal{O}(1)$ time.

- **Underlying Pattern**: `Circular Array Ring Buffer (`rear = (rear + 1) % capacity`)`.

---

## 3. Approach 1 — Naive / Basic Attempt

### Idea
Linear array with `vector.erase(v.begin())` in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
// Linear shift queue O(N)
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ per pop.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Linear memory shifting.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic data structure implementation below directly provides optimal bounds.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
Circular Array Ring Buffer in $\mathcal{O}(1)$ time.

### C++17 Code
```cpp
class CircularQueue {
private:
    int* arr;
    int frontIndex;
    int rearIndex;
    int currentSize;
    int capacity;

public:
    CircularQueue(int cap = 10000) : frontIndex(0), rearIndex(-1), currentSize(0), capacity(cap) {
        arr = new int[capacity];
    }
    
    ~CircularQueue() {
        delete[] arr;
    }
    
    void push(int x) {
        if (isFull()) return;
        rearIndex = (rearIndex + 1) % capacity;
        arr[rearIndex] = x;
        currentSize++;
    }
    
    int pop() {
        if (isEmpty()) return -1;
        int val = arr[frontIndex];
        frontIndex = (frontIndex + 1) % capacity;
        currentSize--;
        return val;
    }
    
    int front() {
        if (isEmpty()) return -1;
        return arr[frontIndex];
    }
    
    int size() {
        return currentSize;
    }
    
    bool isEmpty() {
        return currentSize == 0;
    }
    
    bool isFull() {
        return currentSize == capacity;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ strict time for all methods.
- **Space Complexity**: $\mathcal{O}(N)$ memory.
- **Why this is optimal**: Modulo arithmetic enables circular buffer reuse without moving elements.

---

## 6. Dry Run

Queue ($cap = 3$): `push(1), push(2), push(3), pop(), push(4)`

| Step | Action / State Change | Result |
|---|---|---|
| `push 1, 2, 3` | rear = 2, size = 3 (Full) | `[1, 2, 3]` |
| `pop()` | returns 1, frontIndex = (0+1)%3 = 1, size = 2 | `[_, 2, 3]` |
| `push(4)` | rearIndex = (2+1)%3 = 0, arr[0] = 4, size = 3 | `[4, 2, 3]` (Circular Wrap ✅) |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Queue full (wrap-around push rejected).
- Queue empty (pop returns -1).

### Common Bugs to Avoid
- Forgetting `delete[] arr` in destructor.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is tracking currentSize better than comparing front == rear?**  
  **A**: Comparing `front == rear` is ambiguous because it can represent both an EMPTY queue and a FULL queue. Tracking `currentSize` makes state disambiguation $\mathcal{O}(1)$ and foolproof.


---

## 9. Tags & Related Problems

- **Tags**: `Queue`, `Circular Buffer`, `Arrays`, `Easy`
- **Related problems to practice next**:
- **Implement Stack using Arrays**: Stack counterpart.
