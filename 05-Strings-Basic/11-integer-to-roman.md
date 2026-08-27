# Integer to Roman Conversion (Step 5.2 — Medium String Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Integer to Roman Conversion](https://takeuforward.org/strings/integer-to-roman/)
- **Difficulty**: Medium
- **Statement**: Given an integer $num$ ($1 \le num \le 3999$), convert it to a Roman numeral string.

---

## 1. Problem, Restated

Greedily subtract largest possible Roman base values from $num$ until zero.

- **Input**: Problem parameters.
- **Output**: Transformed string or calculated integer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Define 13 standard Roman denomination pairs (including subtractive cases): `{1000:"M", 900:"CM", 500:"D", 400:"CD", 100:"C", 90:"XC", 50:"L", 40:"XL", 10:"X", 9:"IX", 5:"V", 4:"IV", 1:"I"}`. Greedily subtract the largest fitting denomination and append its string symbol.

- **Underlying Pattern**: `Greedy Denomination Subtraction Table`.

---

## 3. Approach 1 — Brute Force / Naive

### Idea
Digit-by-digit place value mapping with hardcoded thousands/hundreds arrays.

### C++17 Code
```cpp
#include <string>
using namespace std;
string intToRomanPlaceValue(int num) {
    string thousands[] = {"", "M", "MM", "MMM"};
    string hundreds[]  = {"", "C", "CC", "CCC", "CD", "D", "DC", "DCC", "DCCC", "CM"};
    string tens[]      = {"", "X", "XX", "XXX", "XL", "L", "LX", "LXX", "LXXX", "XC"};
    string ones[]      = {"", "I", "II", "III", "IV", "V", "VI", "VII", "VIII", "IX"};
    return thousands[num / 1000] + hundreds[(num % 1000) / 100] + tens[(num % 100) / 10] + ones[num % 10];
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary.
- **Why it's not good enough**: Place value arrays work, but greedy table is standard.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Greedy Denomination Matching with 13 standard pairs.

### C++17 Code
```cpp
#include <string>
#include <vector>
using namespace std;

class Solution {
public:
    string intToRoman(int num) {
        // 13 standard values and their corresponding Roman symbols
        const int values[] = {1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1};
        const string symbols[] = {"M", "CM", "D", "CD", "C", "XC", "L", "XL", "X", "IX", "V", "IV", "I"};
        
        string roman = "";
        for (int i = 0; i < 13 && num > 0; i++) {
            while (num >= values[i]) {
                roman += symbols[i];
                num -= values[i];
            }
        }
        
        return roman;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ bounded (at most 15 subtractions for $num \le 3999$).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Fixed constant number of denomination checks.

---

## 6. Dry Run

$num = 1994$

| Step | Action / State Change | Result |
|---|---|---|
| `num >= 1000` | num -= 1000 (994), add 'M' | roman = "M" |
| `num >= 900` | num -= 900 (94), add 'CM' | roman = "MCM" |
| `num >= 90` | num -= 90 (4), add 'XC' | roman = "MCMXC" |
| `num >= 4` | num -= 4 (0), add 'IV' | roman = "MCMXCIV" ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $num = 3999$ (maximum standard Roman: `"MMMCMXCIX"`).
- $num = 1$ (`"I"`).

### Common Bugs to Avoid
- Missing subtractive pairs (`CM`, `CD`, `XC`, `XL`, `IX`, `IV`) in greedy array.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does greedy strategy always yield the correct Roman numeral?**  
  **A**: Because the Roman numeral system with subtractive pairs satisfies the matroid greedy choice property: taking the largest denomination never restricts future valid representations.


---

## 9. Tags & Related Problems

- **Tags**: `Strings`, `Greedy`, `LeetCode-12`, `Medium`
- **Related problems to practice next**:
- **Roman to Integer**: Inverse conversion.
