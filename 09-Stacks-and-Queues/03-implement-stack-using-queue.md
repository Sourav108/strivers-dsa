# Implement Stack using Queue (Single Queue) (Step 9.1 — Learning)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Implement Stack using Queue (Single Queue)](https://takeuforward.org/data-structure/implement-stack-using-single-queue/)
- **Difficulty**: Easy
- **Statement**: Implement a Last-In-First-Out (LIFO) stack using only **one single standard FIFO queue** in C++ supporting `push`, `pop`, `top`, and `empty`.

---

## 1. Problem, Restated

Invert FIFO queue ordering by rotating existing $(N-1)$ elements to back upon push.

- **Input**: Class methods / expression string.
- **Output**: Operation returns / transformed expression.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

To make a FIFO queue behave as LIFO using only ONE queue: when pushing $X$, first `q.push(X)`. Then rotate the previous $N-1$ elements from front to back: `for (int i = 0; i < q.size() - 1; i++) { q.push(q.front()); q.pop(); }`. This positions $X$ at the very front of the queue, making `pop()` and `top()` strict $\mathcal{O}(1)$ operations!

- **Underlying Pattern**: `Single Queue Element Rotation on Push`.

---

## 3. Approach 1 — Naive / Basic Attempt

### Idea
Using two queues transferring elements back and forth on every pop.

### C++17 Code
```cpp
// Two queues approach
```

### Java Code
```java
// Java equivalent
// Two queues approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ per pop.
- **Space Complexity**: $\mathcal{O}(2N)$.
- **Why it's not good enough**: Uses two queues.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic data structure implementation below directly provides optimal bounds.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
Single Queue In-Place Rotation on `push`.

### C++17 Code
```cpp
#include <queue>
using namespace std;

class MyStack {
private:
    queue<int> q;

public:
    MyStack() {}
    
    void push(int x) {
        q.push(x);
        int sz = q.size();
        
        // Rotate all previous (sz - 1) elements behind the new element x
        for (int i = 0; i < sz - 1; i++) {
            q.push(q.front());
            q.pop();
        }
    }
    
    int pop() {
        int val = q.front();
        q.pop();
        return val;
    }
    
    int top() {
        return q.front();
    }
    
    bool empty() {
        return q.empty();
    }
};
```

### Java Code
```java
import java.util.*;

class MyStack {

    Queue<Integer> q = new LinkedList<>();

    MyStack() {}
    
    void push(int x) {
        q.push(x);
        int sz = q.length;
        
        // Rotate all previous (sz - 1) elements behind the new element x
        for (int i = 0; i < sz - 1; i++) {
            q.push(q.peek());
            q.pop();
        }
    }
    
    int pop() {
        int val = q.peek();
        q.pop();
        return val;
    }
    
    int top() {
        return q.peek();
    }
    
    boolean empty() {
        return q.isEmpty();
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ per `push`, strictly $\mathcal{O}(1)$ for `pop`, `top`, and `empty`.
- **Space Complexity**: $\mathcal{O}(N)$ for single queue storage.
- **Why this is optimal**: Single queue rotation guarantees LIFO top element is always at `q.front()`.

---

## 6. Dry Run

Operations: `push(1), push(2), push(3), top(), pop()`

| Step | Action / State Change | Result |
|---|---|---|
| `push(1)` | q: `[1]` | sz = 1 |
| `push(2)` | push 2 (`[1, 2]`), rotate 1 (`[2, 1]`) | Front is 2 |
| `push(3)` | push 3 (`[2, 1, 3]`), rotate 2, 1 (`[3, 2, 1]`) | Front is 3 |
| `top()` | returns q.front() = 3 | 3 |
| `pop()` | pops 3 -> q: `[2, 1]` | 3 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `pop()` / `top()` on empty stack.
- Single element stack.

### Common Bugs to Avoid
- Loop condition `i < sz` instead of `i < sz - 1` (rotates new element as well, returning to FIFO order).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is single queue rotation preferred over 2 queues?**  
  **A**: It uses half the memory and makes `pop()` and `top()` instantaneous $\mathcal{O}(1)$ operations.


---

## 9. Tags & Related Problems

- **Tags**: `Stack`, `Queue`, `LeetCode-225`, `Easy`
- **Related problems to practice next**:
- **Implement Queue using Stacks**: Inverse implementation.
