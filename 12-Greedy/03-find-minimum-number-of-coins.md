# Find Minimum Number of Coins (Greedy Indian Denominations) (Step 12.1 — Easy)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Find Minimum Number of Coins (Greedy Indian Denominations)](https://takeuforward.org/data-structure/find-minimum-number-of-coins/)
- **Difficulty**: Easy
- **Statement**: Given a value $V$ and standard Indian currency denominations `[1, 2, 5, 10, 20, 50, 100, 200, 500, 2000]`, find the minimum number of coins/notes needed to make change for $V$.

---

## 1. Problem, Restated

Greedily pick the largest denomination $\le V$ repeatedly until $V = 0$.

- **Input**: Parameters specified.
- **Output**: Optimal value / boolean / transformed list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

In a canonical coin system (like Indian currency), every denomination is larger than the sum of smaller denominations that could replace it, or forms a matroid structure. To minimize coins, pick the largest possible denomination $\le V$, subtract it from $V$, and repeat until $V = 0$.

- **Underlying Pattern**: `Greedy Canonical Coin Change`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Recursion testing all combinations in exponential time.

### C++17 Code
```cpp
// Exponential recursion
```

### Java Code
```java
// Java equivalent
// Exponential recursion
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^V)$.
- **Space Complexity**: $\mathcal{O}(V)$.
- **Why it's not good enough**: Exponential search.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard greedy strategy below directly achieves optimal $\mathcal{O}(N \log N)$ or $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Greedy Largest Denomination Subtraction in $\mathcal{O}(D)$ time.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
public:
    vector<int> minPartition(int V) {
        int coins[] = {2000, 500, 200, 100, 50, 20, 10, 5, 2, 1};
        int n = sizeof(coins) / sizeof(coins[0]);
        vector<int> ans;
        
        for (int i = 0; i < n; i++) {
            while (V >= coins[i]) {
                V -= coins[i];
                ans.push_back(coins[i]);
            }
            if (V == 0) break;
        }
        
        return ans;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    int[] minPartition(int V) {
        int coins[] = {2000, 500, 200, 100, 50, 20, 10, 5, 2, 1};
        int n = sizeof(coins) / sizeof(coins[0]);
        List<Integer> ans = new ArrayList<>();
        
        for (int i = 0; i < n; i++) {
            while (V >= coins[i]) {
                V -= coins[i];
                ans.add(coins[i]);
            }
            if (V == 0) break;
        }
        
        return ans;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(D)$ time where $D = 10$ denominations.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Canonical coin system guarantees greedy local choice is globally optimal.

---

## 6. Dry Run

$V = 49$

| Step | Action / State Change | Result |
|---|---|---|
| `Coins >= 50` | 49 < 50 -> skip 2000, 500, 200, 100, 50 | V = 49 |
| `Coin 20` | 49 >= 20 -> pick 20 (V=29) -> pick 20 (V=9) | ans: `[20, 20]` |
| `Coin 5` | 9 >= 5 -> pick 5 (V=4) | ans: `[20, 20, 5]` |
| `Coin 2` | 4 >= 2 -> pick 2 (V=2) -> pick 2 (V=0) | ans: `[20, 20, 5, 2, 2]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $V = 0$ (returns empty).
- Exact match $V = 2000$ (returns `[2000]`).

### Common Bugs to Avoid
- Applying greedy approach to non-canonical coin systems (e.g. `[1, 5, 6]` for $V=10$, greedy yields $6+1+1+1+1=5$ coins, but optimal is $5+5=2$ coins).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does greedy fail for arbitrary coin denominations?**  
  **A**: For arbitrary systems (like `[1, 5, 6]` for $V=10$), greedy takes 6 first, leaving 4 (requires $6+1+1+1+1=5$ coins), missing the global optimum $5+5=2$ coins. Arbitrary systems require DP Coin Change.

- **Q2: What makes a currency system 'canonical'?**  
  **A**: A coin system is canonical if the greedy algorithm always produces an optimal solution. Pearson's $\mathcal{O}(N^3)$ algorithm can verify whether a given denomination set is canonical.

- **Q3: Can division V / coins[i] be used instead of while loop?**  
  **A**: Yes! `int count = V / coins[i]; V %= coins[i]; ans.insert(ans.end(), count, coins[i]);` runs in strict $\mathcal{O}(D)$ iterations.


---

## 9. Tags & Related Problems

- **Tags**: `Greedy`, `Maths`, `Easy`
- **Related problems to practice next**:
- **Coin Change DP**: Arbitrary denominations.
