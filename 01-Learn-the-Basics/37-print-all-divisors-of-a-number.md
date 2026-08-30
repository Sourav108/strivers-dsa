# Print All Divisors of a Number O(sqrt(N)) (Step 1.4 — Know Basic Maths)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Print All Divisors of a Number O(sqrt(N))](https://takeuforward.org/data-structure/print-all-divisors-of-a-given-number/)
- **Difficulty**: Easy
- **Statement**: Given an integer $N$, return all of its positive divisors in sorted order in $\mathcal{O}(\sqrt{N})$ time.

---

## 1. Problem, Restated

Find all factors $d$ such that $N \% d == 0$.

- **Input**: Parameters specified.
- **Output**: Result meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Every divisor $d \le \sqrt{N}$ has a symmetric co-divisor $N / d \ge \sqrt{N}$. By iterating only up to $\sqrt{N}$ (i.e. $i \times i \le N$), we can extract both $i$ and $N / i$ in $\mathcal{O}(\sqrt{N})$ time. If $i == N / i$ (perfect square), insert $i$ once.

- **Underlying Pattern**: `Divisor Symmetry Pair: if $d$ divides $N$, so does $N / d$`.

---

## 3. Approach 1 — Brute Force / Naive

### Idea
Linear scan $1$ to $N$ checking $N \% i == 0$ in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
#include <vector>
using namespace std;
vector<int> printDivisorsLinear(int n) {
    vector<int> divs;
    for (int i = 1; i <= n; i++) {
        if (n % i == 0) divs.push_back(i);
    }
    return divs;
}
```

### Java Code
```java
import java.util.*;

class Solution {
    int[] printDivisorsLinear(int n) {
        List<Integer> divs = new ArrayList<>();
        for (int i = 1; i <= n; i++) {
            if (n % i == 0) divs.add(i);
        }
        return divs;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time — for $N = 10^9$, takes $10^9$ ops $\implies$ TLE.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary.
- **Why it's not good enough**: Fails for large integers.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard mathematical / optimal algorithmic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
Iterate $i \in [1, \sqrt{N}]$, insert pair $(i, N/i)$, then sort divisors.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

vector<int> getAllDivisors(int n) {
    vector<int> divisors;
    
    // Iterate up to sqrt(n)
    for (int i = 1; (long long)i * i <= n; i++) {
        if (n % i == 0) {
            divisors.push_back(i);
            
            // Add symmetric counterpart if distinct
            if (n / i != i) {
                divisors.push_back(n / i);
            }
        }
    }
    
    sort(divisors.begin(), divisors.end()); // O(D log D) where D <= 2*sqrt(N)
    return divisors;
}
```

### Java Code
```java
import java.util.*;

class Solution {
    int[] getAllDivisors(int n) {
        List<Integer> divisors = new ArrayList<>();
        
        // Iterate up to Math.sqrt(n)
        for (int i = 1; (long)i * i <= n; i++) {
            if (n % i == 0) {
                divisors.add(i);
                
                // Add symmetric counterpart if distinct
                if (n / i != i) {
                    divisors.add(n / i);
                }
            }
        }
        
        Arrays.sort(divisors); // O(D log D) where D <= 2sqrt(N)
        return divisors;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\sqrt{N} + D \log D)$ where $D$ is the number of divisors (for $N=10^9$, $\sqrt{N} \approx 31622$ iterations in $< 1\text{ms}$).
- **Space Complexity**: $\mathcal{O}(D)$ to store divisors.
- **Why this is optimal**: Halves the factor search space using divisor pairing.

---

## 6. Dry Run

Divisors of $N = 36$

| Step | Action / State Change | Result |
|---|---|---|
| `$i = 1$` | 36 % 1 == 0 | Add 1 and 36 |
| `$i = 2$` | 36 % 2 == 0 | Add 2 and 18 |
| `$i = 3$` | 36 % 3 == 0 | Add 3 and 12 |
| `$i = 4$` | 36 % 4 == 0 | Add 4 and 9 |
| `$i = 5$` | 36 % 5 != 0 | Skip |
| `$i = 6$` | 36 % 6 == 0 ($6 == 36/6$) | Add 6 once |
| `Result` | Sorted divisors | `[1, 2, 3, 4, 6, 9, 12, 18, 36]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 1$ (returns `[1]`)
- Prime numbers (returns `[1, N]`)
- Perfect squares (avoid duplicate middle divisor).

### Common Bugs to Avoid
- Writing `i <= sqrt(n)` which recomputes `sqrt()` on every loop iteration (write `i * i <= n`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is the maximum number of divisors bounded?**  
  **A**: For $N \le 10^9$, the maximum number of divisors for any integer is at most $1344$, making sorting the divisor list $\mathcal{O}(D \log D)$ negligible.


---

## 9. Tags & Related Problems

- **Tags**: `Maths`, `Number Theory`, `Easy`
- **Related problems to practice next**:
- **Check Prime Number**: Prime testing via sqrt bound.
