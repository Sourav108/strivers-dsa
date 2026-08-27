# Print 1 to N without Loop (Recursion & Backtracking) (Step 1.5 — Learn Basic Recursion)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Print 1 to N without Loop (Recursion & Backtracking)](https://takeuforward.org/recursion/print-1-to-n-using-recursion/)
- **Difficulty**: Easy
- **Statement**: Print numbers from 1 to N without using any loops (`for`, `while`, `do-while`, or `goto`).

---

## 1. Problem, Restated

Use recursion call stack frames (forward parameter passing or backtracking) to print $1$ to $N$.

- **Input**: Parameters specified.
- **Output**: Value or side-effect meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Two paradigms: 1) **Forward Recursion**: pass current counter `f(i+1, n)` printing before the recursive call. 2) **Backtracking (Head Recursion)**: call `f(n-1)` FIRST, and print $n$ AFTER returning from the base case. The call stack reverses the unwinding order naturally!

- **Underlying Pattern**: `Recursive Call Stack Unwinding / Backtracking`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Standard iterative loop (forbidden by problem statement).

### C++17 Code
```cpp
#include <iostream>
using namespace std;
void printLoop(int n) {
    for (int i = 1; i <= n; i++) cout << i << " ";
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Violates no-loop constraint.

---

## 4. Approach 2 — Better

### Idea
Forward recursion with accumulator parameter i.

### C++17 Code
```cpp
#include <iostream>
using namespace std;
void printForward(int i, int n) {
    if (i > n) return;
    cout << i << " ";
    printForward(i + 1, n);
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ stack frames.
- **Why it's still not optimal**: Forward passing works, but backtracking is more fundamental.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Backtracking Recursion: `print1ToN(n-1)` then `cout << n`.

### C++17 Code
```cpp
#include <iostream>
using namespace std;

class Solution {
public:
    void printNos(int N) {
        // Base Case
        if (N == 0) return;
        
        // Recursive call first (delays printing until all smaller numbers print)
        printNos(N - 1);
        
        // Backtracking print step
        cout << N << " ";
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ call stack frames.
- **Why this is optimal**: Clean single-parameter signature leveraging the LIFO call stack.

---

## 6. Dry Run

Backtracking Call Stack Unwinding for $N = 3$

| Step | Action / State Change | Result |
|---|---|---|
| `Push Stack` | printNos(3) -> printNos(2) -> printNos(1) -> printNos(0) | Base case hit at N=0 |
| `Unwind N=1` | printNos(1) prints 1 | Output: `1` |
| `Unwind N=2` | printNos(2) prints 2 | Output: `1 2` |
| `Unwind N=3` | printNos(3) prints 3 | Output: `1 2 3` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 1$ (prints 1)
- $N = 0$ (prints nothing).

### Common Bugs to Avoid
- Missing base case `if (N == 0) return;` causing infinite recursion and stack overflow.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does backtracking reverse order?**  
  **A**: The call stack is LIFO (Last-In-First-Out). Calling `printNos(N-1)` pushes frames until $N=1$. When returning, $N=1$ executes first, followed by $N=2$, then $N=3$.


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Backtracking`, `Easy`
- **Related problems to practice next**:
- **Print N to 1**: Reverse recursion order.
