# Remove K Digits to make Smallest Number (Step 9.3 — Monotonic Stack / Queue)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Remove K Digits to make Smallest Number](https://takeuforward.org/data-structure/remove-k-digits/)
- **Difficulty**: Medium
- **Statement**: Given string `num` representing a non-negative integer and an integer $k$, return the smallest possible integer after removing $k$ digits from `num`.

---

## 1. Problem, Restated

Greedily remove digits creating a monotonic increasing sequence from MSB to LSB.

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Resulting vector of elements / integer answer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

To make a number smaller, digits at higher place values (left) must be minimized. Traverse left-to-right: while $k > 0$ and `stack.top() > digit`, pop stack and decrement $k--$. Push current digit. After loop, if $k > 0$, pop remaining $k$ digits from back. Strip leading zeros. If result is empty, return `"0"`.

- **Underlying Pattern**: `Monotonic Increasing Stack with Removal Budget $k$`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Generate $\binom{N}{k}$ combinations in exponential time.

### C++17 Code
```cpp
// Combination search
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\binom{N}{k})$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Exponential time.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — monotonic data structure below directly achieves optimal $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Monotonic Increasing String Stack in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
#include <string>
using namespace std;

class Solution {
public:
    string removeKdigits(string num, int k) {
        string st = ""; // use string as monotonic stack
        
        for (char digit : num) {
            // Greedily pop larger preceding digits while budget k allows
            while (!st.empty() && k > 0 && st.back() > digit) {
                st.pop_back();
                k--;
            }
            st.push_back(digit);
        }
        
        // If k still > 0, pop trailing digits
        while (k > 0 && !st.empty()) {
            st.pop_back();
            k--;
        }
        
        // Strip leading zeros
        int startIdx = 0;
        while (startIdx < (int)st.length() && st[startIdx] == '0') {
            startIdx++;
        }
        
        string ans = st.substr(startIdx);
        return ans.empty() ? "0" : ans;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ linear time.
- **Space Complexity**: $\mathcal{O}(N)$ string buffer.
- **Why this is optimal**: Each digit is pushed and popped at most once.

---

## 6. Dry Run

`num = "1432219", k = 3`

| Step | Action / State Change | Result |
|---|---|---|
| `Push '1', '4'` | st = "14" | k = 3 |
| `Incoming '3'` | 4 > 3 -> pop '4', push '3', k=2 | st = "13" |
| `Incoming '2'` | 3 > 2 -> pop '3', push '2', k=1 | st = "12" |
| `Incoming '2'` | 2 == 2 -> push '2' | st = "122" |
| `Incoming '1'` | 2 > 1 -> pop '2', push '1', k=0 | st = "121" |
| `Incoming '9'` | k=0 -> push '9' | st = "1219" |
| `Result` | `"1219"` | Smallest number ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $k == N$ (returns `"0"`).
- Result has leading zeros `num = "10200", k = 1` $\implies$ `"200"`.

### Common Bugs to Avoid
- Forgetting to pop trailing digits when $k > 0$ after loop (e.g. `num = "12345", k = 2`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does string act as an optimal stack here?**  
  **A**: C++ `std::string` supports $\mathcal{O}(1)$ `push_back()`, `pop_back()`, and `back()`, avoiding stack-to-string conversion and reversal.


---

## 9. Tags & Related Problems

- **Tags**: `Stack`, `Greedy`, `Monotonic Stack`, `LeetCode-402`, `Medium`
- **Related problems to practice next**:
- **Create Maximum Number**: Advanced greedy stack.
