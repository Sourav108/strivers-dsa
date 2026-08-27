# Lemonade Change (Step 12.1 — Easy)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Lemonade Change](https://takeuforward.org/data-structure/lemonade-change/)
- **Difficulty**: Easy
- **Statement**: At a lemonade stand, each lemonade costs $5. Customers pay with a $5, $10, or $20 bill. You must provide the correct change to each customer starting with no change. Return `true` if you can provide every customer with correct change.

---

## 1. Problem, Restated

Greedily preserve $5 bills by prioritizing giving $10 + $5 as change for a $20 bill.

- **Input**: Parameters specified.
- **Output**: Optimal value / boolean / transformed list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Track counts of `$5` and `$10` bills (we never give $20 as change). 1) Bill is $5: increment `five++`. 2) Bill is $10: needs one $5 change $\implies$ if `five > 0`, `five--`, `ten++`; else return `false`. 3) Bill is $20: needs $15 change. **Greedy Priority**: Give one $10 and one $5 bill if available (`ten > 0 && five > 0`); else give three $5 bills (`five >= 3`). A $5 bill is strictly more versatile than a $10 bill, so always conserve $5 bills!

- **Underlying Pattern**: `Greedy Bill Preservation ($5 bill is most versatile)`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Simulation checking all permutation combinations.

### C++17 Code
```cpp
// Simulation check
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Greedy tracking is optimal.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard greedy strategy below directly achieves optimal $\mathcal{O}(N \log N)$ or $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Greedy $10+$5 Change Priority in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
public:
    bool lemonadeChange(vector<int>& bills) {
        int five = 0, ten = 0;
        
        for (int bill : bills) {
            if (bill == 5) {
                five++;
            } else if (bill == 10) {
                if (five == 0) return false;
                five--;
                ten++;
            } else { // bill == 20 (requires $15 change)
                // Greedy choice: prefer 10 + 5 over 5 + 5 + 5
                if (ten > 0 && five > 0) {
                    ten--;
                    five--;
                } else if (five >= 3) {
                    five -= 3;
                } else {
                    return false; // cannot provide change
                }
            }
        }
        
        return true;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Conserving $5 bills maximizes change capability for future customers.

---

## 6. Dry Run

`bills = [5, 5, 5, 10, 20]`

| Step | Action / State Change | Result |
|---|---|---|
| `Bills 5, 5, 5` | five = 3, ten = 0 | valid |
| `Bill 10` | give 5 -> five = 2, ten = 1 | valid |
| `Bill 20` | prefer 10+5 -> five = 1, ten = 0 | valid |
| `Result` | All customers served | Return TRUE ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- First customer gives 10 or 20 (instant `false`).
- Customer gives 20 when `five=3, ten=0` (gives three $5s).

### Common Bugs to Avoid
- Prioritizing three $5 bills over one $10 and one $5 bill when making change for $20.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is giving a $10 bill instead of two $5 bills strictly better?**  
  **A**: Because a $10 bill can ONLY be used as change for a $20 bill, whereas two $5 bills can be used for either two $10 bills OR one $20 bill. Thus $5 bills have strictly higher future utility!

- **Q2: Can this problem be extended to dynamic bill denominations?**  
  **A**: For arbitrary denominations, we must use a greedy max-heap or DP to find coin change dynamically.

- **Q3: What is the time and space complexity if N = 10^7?**  
  **A**: Time is $\mathcal{O}(N)$ requiring $< 15\text{ms}$, space is strictly $\mathcal{O}(1)$ with two CPU register variables.


---

## 9. Tags & Related Problems

- **Tags**: `Greedy`, `Simulation`, `LeetCode-860`, `Easy`
- **Related problems to practice next**:
- **Assign Cookies**: Greedy matching.
