# If-Else & Switch Statements in C++ (Step 1.1 — Things to Know in C++)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [If-Else & Switch Statements in C++](https://takeuforward.org/c/if-else-statements-in-c/)
- **Difficulty**: Easy
- **Statement**: Implement decision-making constructs in C++ using `if-else`, nested `if`, ternary operator (`? :`), and `switch-case` with fall-through mechanics.

---

## 1. Problem, Restated

Control program execution flow based on boolean expressions and multi-branch jump tables.

---

## 2. Intuition & Pattern

`if-else` chains test conditions sequentially. `switch-case` statements on integral types are optimized by modern compilers into $O(1)$ direct jump tables (branch tables) rather than sequential comparisons.

- **Underlying Pattern**: `Conditional Branching & Jump Table Optimization`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Long Sequential If-Else Chains: Evaluating 10+ discrete cases one by one.

### C++17 Code
```cpp
#include <iostream>
using namespace std;

string getDayNameIfElse(int day) {
    if (day == 1) return "Monday";
    else if (day == 2) return "Tuesday";
    else if (day == 3) return "Wednesday";
    else if (day == 4) return "Thursday";
    else if (day == 5) return "Friday";
    else if (day == 6) return "Saturday";
    else if (day == 7) return "Sunday";
    return "Invalid";
}
```

### Java Code
```java
class Solution {
    String getDayNameIfElse(int day) {
        if (day == 1) return "Monday";
        else if (day == 2) return "Tuesday";
        else if (day == 3) return "Wednesday";
        else if (day == 4) return "Thursday";
        else if (day == 5) return "Friday";
        else if (day == 6) return "Saturday";
        else if (day == 7) return "Sunday";
        return "Invalid";
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ comparisons in worst case.
- **Space Complexity**: $\mathcal{O}(1)$ space.
- **Why it's not good enough**: Evaluates up to $N$ branch conditions sequentially.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Use `switch-case` with compiler jump-table optimization.

### C++17 Code
```cpp
#include <iostream>
#include <string>
using namespace std;

string getDayNameSwitch(int day) {
    switch (day) {
        case 1: return "Monday";
        case 2: return "Tuesday";
        case 3: return "Wednesday";
        case 4: return "Thursday";
        case 5: return "Friday";
        case 6: return "Saturday";
        case 7: return "Sunday";
        default: return "Invalid";
    }
}

int main() {
    int day = 3;
    cout << "Day " << day << ": " << getDayNameSwitch(day) << "\n";
    
    // Ternary operator: concise inline if-else
    int score = 85;
    string result = (score >= 40) ? "Pass" : "Fail";
    cout << "Result: " << result << "\n";
    
    return 0;
}
```

### Java Code
```java
class Solution {
    String getDayNameSwitch(int day) {
        switch (day) {
            case 1: return "Monday";
            case 2: return "Tuesday";
            case 3: return "Wednesday";
            case 4: return "Thursday";
            case 5: return "Friday";
            case 6: return "Saturday";
            case 7: return "Sunday";
            default: return "Invalid";
        }
    }
    
    int main() {
        int day = 3;
        System.out.print("Day " << day << ": " << getDayNameSwitch(day) << "\n");
        
        // Ternary operator: concise if-else
        int score = 85;
        String result = (score >= 40) ? "Pass" : "Fail";
        System.out.print("Result: " << result << "\n");
        
        return 0;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ branch lookup via compiler jump table.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Compiler compiles contiguous switch cases into a direct indirect jump.

---

## 6. Dry Run & Key Reference

Switch Evaluation Trace for `day = 3`

| Step | Action / State Change | Result |
|---|---|---|
| `Lookup` | Compute jump index: day - 1 = 2 | Direct jump to case 3 |
| `Case 3` | return 'Wednesday' | Immediate return ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Missing `break` statement in switch cases causing accidental fall-through.
- Handling invalid default case.
- Ternary operator type mismatch (`condition ? 5 : "hello"` -> compile error).

### Common Bugs to Avoid
- Using floating-point types (`float`/`double`) or `std::string` in a `switch` statement (switch only supports integral and enum types).
- Accidental assignment inside `if` statement: `if (x = 5)` instead of `if (x == 5)`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why can't std::string or float be used in switch statements?**  
  **A**: `switch` requires integral constants so the compiler can generate an integer jump table. Strings and floats require runtime string comparison or IEEE float bit masking.

- **Q2: What is switch fall-through and how does C++17 handle it?**  
  **A**: Without a `break`, execution continues into subsequent cases. C++17 introduces `[[fallthrough]];` attribute to document intentional fall-through and suppress compiler warnings.

- **Q3: How does a compiler jump table work?**  
  **A**: The compiler creates an array of code memory addresses `table[case_val]`. At runtime, it executes `goto table[val]` in $\mathcal{O}(1)$ time.

- **Q4: What is branch prediction penalty in modern CPUs?**  
  **A**: Modern CPUs predict branch directions speculatively. If a branch is mispredicted, the CPU pipeline must be flushed, costing 10–20 clock cycles.


---

## 9. Tags & Related Problems

- **Tags**: `C++ Basics`, `Conditionals`, `Control Flow`, `Easy`
- **Related problems to practice next**:
- **For Loops & Nested Loops**: Loop branching.
- **Functions**: Function control flow.
