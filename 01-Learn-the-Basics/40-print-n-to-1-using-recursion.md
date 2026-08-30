# Print N to 1 using Recursion (Step 1.5 — Learn Basic Recursion)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Print N to 1 using Recursion](https://takeuforward.org/recursion/print-n-to-1-using-recursion/)
- **Difficulty**: Easy
- **Statement**: Print numbers from N down to 1 using recursion without loops.

---

## 1. Problem, Restated

Tail recursion printing current $N$ before delegating to $N-1$.

- **Input**: Parameters specified.
- **Output**: Value or side-effect meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Print $N$ first, then make recursive call `printNos(N - 1)`. Since the recursive call is the last statement, compilers can optimize this into a tail call jump with $\mathcal{O}(1)$ stack space (Tail Call Optimization).

- **Underlying Pattern**: `Tail Recursion (Pre-Order Traversal)`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Loop-based printing.

### C++17 Code
```cpp
#include <iostream>
using namespace std;
void printLoop(int n) {
    for (int i = n; i >= 1; i--) cout << i << " ";
}
```

### Java Code
```java
class Solution {
    void printLoop(int n) {
        for (int i = n; i >= 1; i--) System.out.print(i << " ");
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Violates no-loop constraint.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Tail Recursive Function: `cout << N; printNos(N - 1);`.

### C++17 Code
```cpp
#include <iostream>
using namespace std;

class Solution {
public:
    void printNos(int N) {
        if (N == 0) return;
        
        cout << N << " ";
        printNos(N - 1); // Tail call
    }
};
```

### Java Code
```java
class Solution {

    void printNos(int N) {
        if (N == 0) return;
        
        System.out.print(N << " ");
        printNos(N - 1); // Tail call
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ stack space ($\mathcal{O}(1)$ with Tail Call Optimization -O2).
- **Why this is optimal**: Direct pre-order print.

---

## 6. Dry Run

Trace for $N = 3$

| Step | Action / State Change | Result |
|---|---|---|
| `Call N=3` | Print 3, call printNos(2) | Output: `3` |
| `Call N=2` | Print 2, call printNos(1) | Output: `3 2` |
| `Call N=1` | Print 1, call printNos(0) | Output: `3 2 1` |
| `Base N=0` | Return | Done ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 1$ (prints 1)
- $N = 0$ (returns).

### Common Bugs to Avoid
- Missing base case.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is Tail Call Optimization (TCO)?**  
  **A**: When the recursive call is the final operation, the compiler reuses the current stack frame instead of allocating a new one, transforming recursion into a jump loop.


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Tail Recursion`, `Easy`
- **Related problems to practice next**:
- **Print 1 to N**: Forward recursion.
