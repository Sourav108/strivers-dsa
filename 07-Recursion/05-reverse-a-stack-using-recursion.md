# Reverse a Stack using Recursion (Step 7.1 — Get a Strong Hold)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Reverse a Stack using Recursion](https://takeuforward.org/recursion/reverse-a-stack-using-recursion/)
- **Difficulty**: Medium
- **Statement**: Given a stack of integers, reverse the stack in-place using recursion only, without using loops (`for`, `while`) or additional stacks/queues.

---

## 1. Problem, Restated

Reverse stack using recursive `insertAtBottom` function.

- **Input**: Parameters specified.
- **Output**: Value or vector of combinations meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) `reverseStack(st)`: Pop top element `x`, reverse the remaining stack `reverseStack(st)`, then insert `x` at the very bottom via `insertAtBottom(st, x)`. 2) `insertAtBottom(st, x)`: If stack is empty, `st.push(x)`. Otherwise, pop `top`, call `insertAtBottom(st, x)`, and push `top` back.

- **Underlying Pattern**: `Recursive Pop + Recursive InsertAtBottom`.

---

## 3. Approach 1 — Naive / Pitfall

### Idea
Using an auxiliary queue or array in $\mathcal{O}(N)$ memory.

### C++17 Code
```cpp
// Queue approach
```

### Java Code
```java
// Java equivalent
// Queue approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Violates no-extra-data-structure constraint.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic recursion/backtracking below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Double Recursion: `reverseStack` + `insertAtBottom` in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
#include <stack>
using namespace std;

class Solution {
private:
    void insertAtBottom(stack<int>& st, int element) {
        if (st.empty()) {
            st.push(element);
            return;
        }
        
        int topElement = st.top();
        st.pop();
        insertAtBottom(st, element);
        st.push(topElement);
    }

public:
    void reverseStack(stack<int>& st) {
        if (st.empty()) return;
        
        int topElement = st.top();
        st.pop();
        
        // Reverse remaining stack
        reverseStack(st);
        
        // Insert popped element at the bottom
        insertAtBottom(st, topElement);
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    void insertAtBottom(Stack<Integer> st, int element) {
        if (st.isEmpty()) {
            st.push(element);
            return;
        }
        
        int topElement = st.peek();
        st.pop();
        insertAtBottom(st, element);
        st.push(topElement);
    }

    void reverseStack(Stack<Integer> st) {
        if (st.isEmpty()) return;
        
        int topElement = st.peek();
        st.pop();
        
        // Reverse remaining stack
        reverseStack(st);
        
        // Insert popped element at the bottom
        insertAtBottom(st, topElement);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion call stack space.
- **Why this is optimal**: Reverses stack purely through LIFO stack frame unwinding.

---

## 6. Dry Run

Stack (top to bottom): `[1, 2, 3]`

| Step | Action / State Change | Result |
|---|---|---|
| `Unwind` | Pop 1, 2, 3 -> stack empty | Base reached |
| `insertAtBottom(3)` | st = [3] | Bottom |
| `insertAtBottom(2)` | pop 3, push 2, push 3 -> st = [3, 2] | 2 inserted at bottom |
| `insertAtBottom(1)` | pop 3, pop 2, push 1, push 2, push 3 -> st = [3, 2, 1] | Reversed! ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Empty stack.
- Single element stack.

### Common Bugs to Avoid
- Missing `st.push(topElement)` during backtracking return in `insertAtBottom`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Can a stack be reversed in O(N) time without extra memory?**  
  **A**: No, with a single stack and $\mathcal{O}(1)$ space, $\mathcal{O}(N^2)$ is optimal. With a Linked List implementation of stack, reversal is $\mathcal{O}(N)$.


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Stack`, `Medium`
- **Related problems to practice next**:
- **Sort a Stack**: Stack recursion.
