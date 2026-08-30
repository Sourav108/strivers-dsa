# String to Integer (atoi) (Step 18.1 — String Matching & Hard Algorithms)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [String to Integer (atoi)](https://takeuforward.org/strings/implement-atoi/)
- **Difficulty**: Medium
- **Statement**: Implement the `myAtoi(string s)` function, which converts a string to a 32-bit signed integer (similar to C/C++'s `atoi` function). The algorithm for `myAtoi(string s)` is as follows: (1) Whitespace: Ignore any leading whitespace (`" "`). (2) Signedness: Determine the sign by checking if the next character is `'-'` or `'+'`, assuming positivity if neither present. (3) Conversion: Read in next the characters until the next non-digit character or the end of the input is reached. The rest of the string is ignored. (4) Rounding: If the integer is out of the 32-bit signed integer range $[-2^{31}, 2^{31} - 1]$, then clamp the integer so that it remains in the range. Specifically, integers less than $-2^{31}$ should be clamped to $-2^{31}$, and integers greater than $2^{31} - 1$ should be clamped to $2^{31} - 1$. Return the integer as the final result.

---

## 1. Problem, Restated

Parse a string into a 32-bit signed integer with robust handling of leading whitespace, optional signs, trailing non-digits, and integer clamping on $[-2^{31}, 2^{31}-1]$ in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

- **Input**: String `s`.
- **Output**: 32-bit parsed integer.
- **Complexity Goal**: $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

---

## 2. Intuition & Pattern

**The 4-State Parsing Pipeline**: 
1) **Skip Leading Whitespace**: 
   Advance index while `s[i] == ' '`. 
2) **Parse Sign**: 
   If `s[i] == '-'`, set `sign = -1`, `i++`. Else if `s[i] == '+'`, set `sign = +1`, `i++`. 
3) **Digit Conversion & 32-Bit Overflow Prevention**: 
   While `isdigit(s[i])`: 
   - Current digit $D = s[i] - \text{'0'}$. 
   - Before multiplying `ans * 10`, check if it will overflow `INT_MAX` ($2147483647$): 
     $$\text{if } (\text{ans} > \text{INT\_MAX} / 10) \lor (\text{ans} == \text{INT\_MAX} / 10 \land D > 7):$$ 
     - If `sign == 1`: return `INT_MAX`. 
     - If `sign == -1`: return `INT_MIN` ($-2147483648$). 
   - Safe to accumulate: `ans = ans * 10 + D`, `i++`. 
4) Return `sign * ans`. 
**Complexity**: Single pass in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

- **Underlying Pattern**: `Deterministic Finite Automaton (DFA) / Overflow Pre-Check Invariant`.

---

## 3. Approach 1 — Naive (Standard String Stream)

### Idea
Use string streams or standard library `stoi()` which throws exceptions on overflow or bad formats.

### C++17 Code
```cpp
#include <string>
#include <sstream>
using namespace std;

class SolutionLibrary {
public:
    int myAtoi(string s) {
        stringstream ss(s);
        long long num = 0;
        ss >> num;
        if (num > INT_MAX) return INT_MAX;
        if (num < INT_MIN) return INT_MIN;
        return num;
    }
};
```

### Java Code
```java
class SolutionLibrary {

    int myAtoi(String s) {
        stringstream ss(s);
        long num = 0;
        ss >> num;
        if (num > Integer.MAX_VALUE) return Integer.MAX_VALUE;
        if (num < Integer.MIN_VALUE) return Integer.MIN_VALUE;
        return num;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ stream buffer space.
- **Why it's not good enough**: Fails to satisfy interview requirement of implementing custom robust parsing without library streams or exceptions.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard sequential parsing with overflow pre-checking below directly achieves optimal $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Sequential Parsing with Overflow Pre-Check)

### Idea
Deterministic Sequential Parsing with Overflow Pre-Check in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <string>
#include <climits>
using namespace std;

class Solution {
public:
    int myAtoi(string s) {
        int n = s.size();
        int i = 0;
        
        // Step 1: Skip leading whitespace
        while (i < n && s[i] == ' ') {
            i++;
        }
        
        if (i == n) return 0; // String contains only whitespace
        
        // Step 2: Check optional sign
        int sign = 1;
        if (s[i] == '-') {
            sign = -1;
            i++;
        } else if (s[i] == '+') {
            sign = 1;
            i++;
        }
        
        // Step 3: Convert digits with 32-bit overflow prevention
        int result = 0;
        while (i < n && s[i] >= '0' && s[i] <= '9') {
            int digit = s[i] - '0';
            
            // Check for potential overflow before computing result * 10 + digit
            // INT_MAX = 2147483647 (ends in 7), INT_MIN = -2147483648 (ends in 8)
            if (result > INT_MAX / 10 || (result == INT_MAX / 10 && digit > 7)) {
                return (sign == 1) ? INT_MAX : INT_MIN;
            }
            
            result = result * 10 + digit;
            i++;
        }
        
        return sign * result;
    }
};
```

### Java Code
```java
class Solution {

    int myAtoi(String s) {
        int n = s.length;
        int i = 0;
        
        // Step 1: Skip leading whitespace
        while (i < n && s[i] == ' ') {
            i++;
        }
        
        if (i == n) return 0; // String contains only whitespace
        
        // Step 2: Check optional sign
        int sign = 1;
        if (s[i] == '-') {
            sign = -1;
            i++;
        } else if (s[i] == '+') {
            sign = 1;
            i++;
        }
        
        // Step 3: Convert digits with 32-bit overflow prevention
        int result = 0;
        while (i < n && s[i] >= '0' && s[i] <= '9') {
            int digit = s[i] - '0';
            
            // Check for potential overflow before computing result * 10 + digit
            // Integer.MAX_VALUE = 2147483647 (ends in 7), Integer.MIN_VALUE = -2147483648 (ends in 8)
            if (result > Integer.MAX_VALUE / 10 || (result == Integer.MAX_VALUE / 10 && digit > 7)) {
                return (sign == 1) ? Integer.MAX_VALUE : Integer.MIN_VALUE;
            }
            
            result = result * 10 + digit;
            i++;
        }
        
        return sign * result;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time (single pass through string of length $N$).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space (only scalar integer variables).
- **Why this is optimal**: Overflow pre-check allows safe 32-bit arithmetic without requiring 64-bit integer extensions or dynamic allocations.

---

## 6. Dry Run

`s = "   -042abc"` ($N = 10$)

| Step | Action / State Change | Result |
|---|---|---|
| `Step 1: Whitespace` | Skip indices 0, 1, 2 (' '). Pointer moves to $i = 3$ | i = 3 ('-') |
| `Step 2: Sign` | $s[3] == '-'$ $\implies sign = -1, i = 4$ | sign = -1, i = 4 |
| `Step 3: Leading '0'` | $s[4] == '0' \implies result = 0 * 10 + 0 = 0, i = 5$ | result = 0 |
| `Step 4: Digit '4'` | $s[5] == '4' \implies result = 0 * 10 + 4 = 4, i = 6$ | result = 4 |
| `Step 5: Digit '2'` | $s[6] == '2' \implies result = 4 * 10 + 2 = 42, i = 7$ | result = 42 |
| `Step 6: Non-digit 'a'` | $s[7] == 'a'$ (not a digit) $\implies$ loop terminates! | Terminated |
| `Result` | Return $sign \times result = -1 \times 42 = -42$ | Output = -42 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Number exceeds `INT_MAX` `"91283472332"` (returns `INT_MAX`).
- Number exceeds `INT_MIN` `"-91283472332"` (returns `INT_MIN`).
- Words before digits `"words and 987"` (returns 0).
- Only signs `"+"` or `"-"` (returns 0).

### Common Bugs to Avoid
- Multiplying `result * 10` before checking overflow (causes signed integer overflow undefined behavior).
- Treating `'+'` as non-digit termination when it appears at the start.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is the condition `(result == INT_MAX / 10 && digit > 7)` mathematically necessary?**  
  **A**: Because `INT_MAX` is $2147483647$ ($2^{31}-1$). If `result == 214748364`, appending any digit $> 7$ (such as 8 or 9) results in $2147483648$, which exceeds `INT_MAX`! For negative numbers, $-2147483648$ clamps to `INT_MIN` directly!

- **Q2: How to model atoi as a formal Deterministic Finite Automaton (DFA)?**  
  **A**: A DFA has 4 states: `State 0 (Start / Whitespace)`, `State 1 (Signed)`, `State 2 (In Number / Accumulating)`, and `State 3 (End / Rejected)`. A transition matrix `table[state][char_type]` drives the parser with $\mathcal{O}(1)$ state lookups!

- **Q3: What if the target platform is an embedded system with NO 64-bit integers (strictly 32-bit registers)?**  
  **A**: Our implementation operates purely on standard 32-bit `int` registers using division pre-checking `result > INT_MAX / 10` without ever declaring a 64-bit `long long`!


---

## 9. Tags & Related Problems

- **Tags**: `Strings`, `Math`, `Parsing`, `DFA`, `LeetCode-8`, `Medium`
- **Related problems to practice next**:
- **Valid Number**: DFA state machine.
- **Reverse Integer**: Integer bounds.
