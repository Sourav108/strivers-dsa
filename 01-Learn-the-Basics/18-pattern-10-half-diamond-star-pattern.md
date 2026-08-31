# Pattern-10: Half Diamond Star Pattern (Step 1.2 — Build-up Logical Thinking (Patterns))

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Pattern-10: Half Diamond Star Pattern](https://takeuforward.org/strivers-a2z-dsa-course/must-do-pattern-problems-must-do-questions)
- **Difficulty**: Easy
- **Statement**: Print a sideways half-diamond of $2N - 1$ rows with peak at row $N$.

---

## 1. Problem, Restated

Construct pattern with nested loops using coordinate math and symmetry.

- **Input**: Problem parameters.
- **Output**: Formatted pattern string / computed numerical result.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Stars at row $i$: if $i \le N$, $i$ stars; else $2N - i$ stars.

- **Underlying Pattern**: `2D Grid Geometry & Symmetry Mapping`.

---

## 3. Approach 1 — Brute Force / Naive

### Idea
Hardcoding rows or using 1D strings without structured loop invariants.

### C++17 Code
```cpp
void pattern10(int n) {
    for (int i = 1; i <= 2 * n - 1; i++) {
        int stars = (i <= n) ? i : (2 * n - i);
        for (int j = 1; j <= stars; j++) {
            cout << "* ";
        }
        cout << "\n";
    }
}
```

### Java Code
```java
class Solution {
    void pattern10(int n) {
        for (int i = 1; i <= 2 * n - 1; i++) {
            int stars = (i <= n) ? i : (2 * n - i);
            for (int j = 1; j <= stars; j++) {
                System.out.print("* ");
            }
            System.out.println();
        }
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ — prints $N \times N$ grid cells.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why it's not good enough**: Optimal bound for generating $N^2$ character outputs.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard mathematical / idiomatic approach below directly resolves the problem.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
Nested loops with parameterized symmetry formula.

### C++17 Code
```cpp
#include <iostream>
#include <algorithm>
using namespace std;

void pattern10(int n) {
    for (int i = 1; i <= 2 * n - 1; i++) {
        int stars = (i <= n) ? i : (2 * n - i);
        for (int j = 1; j <= stars; j++) {
            cout << "* ";
        }
        cout << "\n";
    }
}

int main() {
    int n = 4;
    pattern10(n);
    return 0;
}
```

### Java Code
```java
class Solution {
    void pattern10(int n) {
        for (int i = 1; i <= 2 * n - 1; i++) {
            int stars = (i <= n) ? i : (2 * n - i);
            for (int j = 1; j <= stars; j++) {
                System.out.print("* ");
            }
            System.out.println();
        }
    }
    
    public static void main(String[] args) {
        int n = 4;
        pattern10(n);
        return 0;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Direct mathematical coordinate calculation avoids extra storage.

---

## 6. Dry Run

Dry Run Trace for $N = 4$

| Step | Action / State Change | Result |
|---|---|---|
| `Row 0` | Computes spaces and characters | 1, 2, 3, ..., N, ..., 2, 1 stars. |
| `Row 1..N-1` | Symmetrical progression | Complete visual pattern ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- N = 1 (single character output).
- N = 0 (empty output).

### Common Bugs to Avoid
- Missing `\n` newline after inner loop.
- Off-by-one errors in loop boundaries (`<` vs `<=`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is the general 4-step framework for solving any pattern problem?**  
  **A**: 1) Count total rows (outer loop). 2) Identify columns, spaces, and character count per row. 3) Print row contents. 4) Add newline and verify symmetry.

- **Q2: Why is Pattern 22 (Concentric Matrix) solved using distance from edges?**  
  **A**: The value at $(i, j)$ is determined by the minimum Manhattan distance to any of the 4 borders: $\min(i, j, 2n-2-i, 2n-2-j)$. Subtracting this distance from $N$ yields the concentric ring value directly in $\mathcal{O}(1)$ without simulation!

- **Q3: How to handle trailing spaces cleanly in competitive programming?**  
  **A**: Most online judges ignore trailing whitespaces before newlines, but omitting unnecessary trailing spaces is good practice.


---

## 9. Tags & Related Problems

- **Tags**: `C++ Basics`, `Patterns`, `Geometry`, `Easy`
- **Related problems to practice next**:
- **For Loops & Nested Loops**: Loop mechanics.
- **Arrays and 2D Arrays**: Matrix mapping.
