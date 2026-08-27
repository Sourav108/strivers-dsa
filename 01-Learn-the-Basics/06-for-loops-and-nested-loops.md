# For Loops & Nested Loops in C++ (Step 1.1 — Things to Know in C++)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [For Loops & Nested Loops in C++](https://takeuforward.org/c/for-loops-in-c/)
- **Difficulty**: Easy
- **Statement**: Master iteration in C++ using traditional three-expression `for` loops, range-based `for` loops (C++11/C++17), loop invariants, pre-increment vs post-increment, and nested loop complexity.

---

## 1. Problem, Restated

Understand loop execution sequence (Init -> Condition -> Body -> Update), nested loop grid coordinates, and range-for loops with const reference.

---

## 2. Intuition & Pattern

A `for` loop executes a statement repeatedly until a condition evaluates to false. In nested loops, the inner loop executes completely for each single iteration of the outer loop, resulting in multiplicative time complexity $\mathcal{O}(N \times M)$. In C++, `++i` (pre-increment) is preferred over `i++` (post-increment) because it avoids creating an unnecessary temporary copy object.

- **Underlying Pattern**: `Iteration Mechanics & Loop Invariant Verification`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Post-increment with by-value range loops: creating temporary copies on each iteration.

### C++17 Code
```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

void processStringsSlow(const vector<string>& vec) {
    // By-value range-for copies every string!
    for (string s : vec) {
        cout << s << " ";
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times L)$ due to deep copying of each string of length $L$.
- **Space Complexity**: $\mathcal{O}(L)$ temporary copy space.
- **Why it's not good enough**: Range-for by value triggers copy constructors for non-primitive types.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Use `const auto&` range-for and pre-increment `++i`.

### C++17 Code
```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

int main() {
    // Standard 3-expression for loop
    for (int i = 0; i < 5; ++i) {
        cout << i << " ";
    }
    cout << "\n";
    
    // Nested loops: Matrix coordinate traversal
    int n = 3;
    for (int r = 0; r < n; ++r) {
        for (int c = 0; c < n; ++c) {
            cout << "(" << r << "," << c << ") ";
        }
        cout << "\n";
    }
    
    // C++17 Range-based for loop with const reference (Zero-copy)
    vector<string> names = {"Alice", "Bob", "Charlie"};
    for (const auto& name : names) {
        cout << name << "\n";
    }
    
    return 0;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ linear time with zero memory copy overhead.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary memory.
- **Why this is optimal**: `const auto&` binds directly to existing memory without copying.

---

## 6. Dry Run & Key Reference

Nested Loop Coordinate Sequence ($3 \times 3$ Grid)

| Step | Action / State Change | Result |
|---|---|---|
| `r = 0` | c runs 0, 1, 2 | (0,0) (0,1) (0,2) |
| `r = 1` | c runs 0, 1, 2 | (1,0) (1,1) (1,2) |
| `r = 2` | c runs 0, 1, 2 | (2,0) (2,1) (2,2) |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Loop condition never met (0 iterations).
- Modifying vector inside range-based for loop (invalidates iterators $\implies$ undefined behavior).
- Unsigned loop termination (`for (unsigned i = 5; i >= 0; --i)` causes infinite loop because unsigned is always $\ge 0$!).

### Common Bugs to Avoid
- Writing `for (unsigned int i = n - 1; i >= 0; i--)` which underflows to `4294967295` instead of stopping at -1.
- Off-by-one errors: `i <= n` vs `i < n` on 0-indexed arrays.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is ++i faster than i++ in C++?**  
  **A**: `++i` modifies the value in place and returns the reference. `i++` must create a copy of the old value, increment the original, and return the copy. For custom iterator classes, this copy constructor can be expensive.

- **Q2: Why does for (unsigned i = 5; i >= 0; i--) cause an infinite loop?**  
  **A**: Because unsigned integers cannot represent negative values. When $i = 0$, `i--` wraps around to `UINT_MAX`, so `i >= 0` is ALWAYS true!

- **Q3: What is structured binding in C++17 for loops?**  
  **A**: For iterating maps: `for (const auto& [key, val] : map)` unpacks pair tuples directly.


---

## 9. Tags & Related Problems

- **Tags**: `C++ Basics`, `Loops`, `Control Flow`, `Easy`
- **Related problems to practice next**:
- **While & Do-While Loops**: Alternative loop structures.
- **Patterns**: 22 visual loop patterns.
