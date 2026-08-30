# Sort a Stack using Recursion (Step 7.1 — Get a Strong Hold)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Sort a Stack using Recursion](https://takeuforward.org/recursion/sort-a-stack-using-recursion/)
- **Difficulty**: Medium
- **Statement**: Given a stack of integers, sort the stack in ascending order (smallest at bottom, largest at top) using recursion only, without using loops (`for`, `while`) or additional data structures.

---

## 1. Problem, Restated

Sort stack elements purely through recursive call stack frames.

- **Input**: Parameters specified.
- **Output**: Value or vector of combinations meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Two recursive functions: 1) `sortStack(st)`: Pop top element `x`, recursively sort remaining stack `sortStack(st)`, then insert `x` in sorted position via `sortedInsert(st, x)`. 2) `sortedInsert(st, x)`: If stack is empty or `x >= st.top()`, push `x`. Otherwise, pop `top`, call `sortedInsert(st, x)`, and push `top` back.

- **Underlying Pattern**: `Recursive Pop + Recursive Sorted Insert`.

---

## 3. Approach 1 — Naive / Pitfall

### Idea
Dump to vector, sort, push back.

### C++17 Code
```cpp
// Vector sort
```

### Java Code
```java
// Java equivalent
// Vector sort
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Violates no-extra-data-structure constraint.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic recursion/backtracking below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Double Recursion: `sortStack` + `sortedInsert`.

### C++17 Code
```cpp
#include <stack>
using namespace std;

class Solution {
private:
    void sortedInsert(stack<int>& st, int element) {
        // Base condition: empty stack or element is greater than current top
        if (st.empty() || element >= st.top()) {
            st.push(element);
            return;
        }
        
        // Pop top and recurse
        int topElement = st.top();
        st.pop();
        sortedInsert(st, element);
        
        // Backtracking push back
        st.push(topElement);
    }

public:
    void sortStack(stack<int>& st) {
        if (st.empty()) return;
        
        int topElement = st.top();
        st.pop();
        
        // Sort remaining stack
        sortStack(st);
        
        // Insert top element in sorted stack
        sortedInsert(st, topElement);
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    void sortedInsert(Stack<Integer> st, int element) {
        // Base condition: empty stack or element is greater than current top
        if (st.isEmpty() || element >= st.peek()) {
            st.push(element);
            return;
        }
        
        // Pop top and recurse
        int topElement = st.peek();
        st.pop();
        sortedInsert(st, element);
        
        // Backtracking push back
        st.push(topElement);
    }

    void sortStack(Stack<Integer> st) {
        if (st.isEmpty()) return;
        
        int topElement = st.peek();
        st.pop();
        
        // Sort remaining stack
        sortStack(st);
        
        // Insert top element in sorted stack
        sortedInsert(st, topElement);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time ($N$ elements $\times N$ inserts).
- **Space Complexity**: $\mathcal{O}(N)$ recursion call stack space.
- **Why this is optimal**: Pure recursion with zero auxiliary data structures.

---

## 6. Dry Run

Stack (top to bottom): `[3, 1, 4, 2]`

| Step | Action / State Change | Result |
|---|---|---|
| `Unwind` | Pop 3, 1, 4, 2 -> stack empty | Base condition reached |
| `Insert 2` | st = [2] | sorted |
| `Insert 4` | 4 >= 2 -> st = [4, 2] | sorted |
| `Insert 1` | pop 4, pop 2, push 1, push 2, push 4 -> st = [4, 2, 1] | sorted |
| `Insert 3` | pop 4, push 3, push 4 -> st = [4, 3, 2, 1] | Sorted Stack (top to bottom: 4, 3, 2, 1) ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Empty stack.
- Already sorted stack.
- Stack with duplicate elements.

### Common Bugs to Avoid
- Missing base condition in `sortedInsert` (`st.empty()`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is this algorithm analogous to Insertion Sort?**  
  **A**: Because `sortedInsert` finds the correct position for an element in an already sorted subarray, mimicking the shift-and-insert pass of Insertion Sort!


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Stack`, `Sorting`, `Medium`
- **Related problems to practice next**:
- **Reverse a Stack**: Stack reversal recursion.
