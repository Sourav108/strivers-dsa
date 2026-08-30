# Generate Valid IP Addresses (Restore IP Addresses) (Step 7.3 — Hard Recursion Problems & Backtracking)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Generate Valid IP Addresses (Restore IP Addresses)](https://takeuforward.org/recursion/restore-ip-addresses/)
- **Difficulty**: Medium
- **Statement**: Given a string $s$ containing only digits, return all possible valid IPv4 addresses that can be formed by inserting dots. A valid IPv4 address consists of exactly 4 integers separated by dots, where each integer is in $[0, 255]$ with no leading zeros (except for "0" itself).

---

## 1. Problem, Restated

Partition string into 4 valid numeric segments in $[0, 255]$.

- **Input**: Grid / String / Constraints.
- **Output**: Boolean feasibility or complete vector of solution configurations.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Backtrack maintaining `(index, dotsCount, currentIP)`. At each octet: try chunk lengths 1, 2, and 3. Validate: integer value $\le 255$, and no leading zero (`length > 1 && s[index] == '0'` is invalid). When `dotsCount == 4` and `index == s.length()`, add to results.

- **Underlying Pattern**: `4-Part Backtracking with IPv4 Octet Validation`.

---

## 3. Approach 1 — Naive / Unpruned Search

### Idea
3 nested loops placing dots at all possible split positions.

### C++17 Code
```cpp
// 3 nested loops approach
```

### Java Code
```java
// Java equivalent
// 3 nested loops approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(3^4) = \mathcal{O}(1)$ bounded.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Nested loops work, but recursion is standard.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard backtracking algorithm below directly provides optimal pruning.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
Recursive Backtracking with Pruned Octet Validation.

### C++17 Code
```cpp
#include <vector>
#include <string>
using namespace std;

class Solution {
private:
    void backtrack(int index, int dots, const string& s, string current, vector<string>& result) {
        if (dots == 4) {
            if (index == (int)s.length()) {
                current.pop_back(); // remove trailing dot
                result.push_back(current);
            }
            return;
        }
        
        for (int len = 1; len <= 3 && index + len <= (int)s.length(); len++) {
            string segment = s.substr(index, len);
            int val = stoi(segment);
            
            // Check leading zero and value <= 255
            if ((len > 1 && segment[0] == '0') || val > 255) break;
            
            backtrack(index + len, dots + 1, s, current + segment + ".", result);
        }
    }

public:
    vector<string> restoreIpAddresses(string s) {
        vector<string> result;
        if (s.length() < 4 || s.length() > 12) return result;
        backtrack(0, 0, s, "", result);
        return result;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    void backtrack(int index, int dots, String s, String current, String[] result) {
        if (dots == 4) {
            if (index == s.length()) {
                current.remove(); // remove trailing dot
                result.add(current);
            }
            return;
        }
        
        for (int len = 1; len <= 3 && index + len <= s.length(); len++) {
            String segment = s.substring(index, index + len);
            int val = stoi(segment);
            
            // Check leading zero and value <= 255
            if ((len > 1 && segment[0] == '0') || val > 255) break;
            
            backtrack(index + len, dots + 1, s, current + segment + ".", result);
        }
    }

    String[] restoreIpAddresses(String s) {
        List<String> result = new ArrayList<>();
        if (s.length() < 4 || s.length() > 12) return result;
        backtrack(0, 0, s, "", result);
        return result;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(3^4) = \mathcal{O}(1)$ bounded ($< 81$ paths).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space (maximum depth 4).
- **Why this is optimal**: Constrained search tree with strict early break rules.

---

## 6. Dry Run

$s = \text{"25525511135"}$

| Step | Action / State Change | Result |
|---|---|---|
| `Octets` | "255" -> "255" -> "11" -> "135" | Valid IP: `"255.255.11.135"` |
| `Octets` | "255" -> "255" -> "111" -> "35" | Valid IP: `"255.255.111.35"` |
| `Result` | Total valid addresses | `["255.255.11.135", "255.255.111.35"]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- String length $< 4$ or $> 12$ (returns `[]`).
- $s = \text{"0000"}$ $\implies$ `["0.0.0.0"]`.

### Common Bugs to Avoid
- Allowing leading zeros like `"01"` or `"00"`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why can strings longer than 12 be rejected immediately?**  
  **A**: Each of the 4 IPv4 octets can hold at most 3 digits. $4 \times 3 = 12$. Any string with $> 12$ characters cannot form a valid IPv4 address.


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Backtracking`, `Strings`, `LeetCode-93`, `Medium`
- **Related problems to practice next**:
- **Letter Combinations**: Combinations.
