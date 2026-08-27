# While & Do-While Loops in C++ (Step 1.1 — Things to Know in C++)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [While & Do-While Loops in C++](https://takeuforward.org/c/while-loops-in-c/)
- **Difficulty**: Easy
- **Statement**: Understand entry-controlled (`while`) and exit-controlled (`do-while`) loop semantics, infinite loop avoidance, loop control keywords (`break`, `continue`), and sentinel-controlled loops.

---

## 1. Problem, Restated

Compare pre-test (`while`) vs post-test (`do-while`) execution guarantees and loop invariants.

---

## 2. Intuition & Pattern

`while` checks condition BEFORE entering the loop body (0 or more executions). `do-while` executes the body FIRST before checking the condition (guaranteed $\ge 1$ execution). `break` terminates the loop immediately, while `continue` skips the rest of the current iteration and jumps to the next test.

- **Underlying Pattern**: `Entry-Controlled vs Exit-Controlled Iteration`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Infinite while loop without progress step.

### C++17 Code
```cpp
#include <iostream>
using namespace std;

void infiniteLoopBug() {
    int i = 0;
    while (i < 10) {
        if (i == 5) continue; // BUG: skips i++, causing infinite loop at i=5!
        cout << i << " ";
        i++;
    }
}
```

### Complexity Derivation
- **Time Complexity**: Infinite loop / TLE.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Missing update step prior to `continue`.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Proper update discipline and do-while for guaranteed first-run menus/prompts.

### C++17 Code
```cpp
#include <iostream>
using namespace std;

int main() {
    // Standard while loop (Entry-controlled: 0 or more times)
    int n = 12345;
    int digitCount = 0;
    while (n > 0) {
        digitCount++;
        n /= 10; // Progress toward termination
    }
    cout << "Digits: " << digitCount << "\n";
    
    // do-while loop (Exit-controlled: Guaranteed >= 1 execution)
    int num = 0;
    do {
        cout << "Runs at least once even when num == 0! Val: " << num << "\n";
        num++;
    } while (num < 0); // Condition is false, but body executed once!
    
    return 0;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log_{10} N)$ for digit extraction.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Guaranteed loop termination and robust invariant maintenance.

---

## 6. Dry Run & Key Reference

Digit Extraction Trace for `n = 12345`

| Step | Action / State Change | Result |
|---|---|---|
| `Iter 1` | n = 12345 -> digit = 5, n = 1234 | count = 1 |
| `Iter 2` | n = 1234  -> digit = 4, n = 123 | count = 2 |
| `Iter 3` | n = 123   -> digit = 3, n = 12 | count = 3 |
| `Iter 4` | n = 12    -> digit = 2, n = 1 | count = 4 |
| `Iter 5` | n = 1     -> digit = 1, n = 0 | count = 5 |
| `Exit` | n == 0 -> while loop ends | Total digits = 5 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Input $n = 0$ in digit extraction: `while (n > 0)` gives 0 digits (use `do-while` or `n == 0 ? 1 : ...`).
- `do-while` trailing semicolon (`do { ... } while (cond);`).

### Common Bugs to Avoid
- Forgetting semicolon after `do { ... } while (condition);` (syntax error).
- Calling `continue` before updating loop variables in a `while` loop.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: When should do-while be preferred over while?**  
  **A**: When the operation must execute at least once regardless of input (e.g. user menu prompts, retry logic with backoff, digit counting for $N=0$).

- **Q2: What is the difference between break and continue?**  
  **A**: `break` exits the enclosing loop entirely. `continue` skips remaining statements in the current iteration and jumps directly to the condition test.


---

## 9. Tags & Related Problems

- **Tags**: `C++ Basics`, `Loops`, `Control Flow`, `Easy`
- **Related problems to practice next**:
- **For Loops & Nested Loops**: Bounded iteration.
- **Count Digits in a Number**: Mathematical while loop application.
