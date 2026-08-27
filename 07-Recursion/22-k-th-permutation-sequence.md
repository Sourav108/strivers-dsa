# K-th Permutation Sequence (Step 7.3 — Hard Recursion Problems & Backtracking)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [K-th Permutation Sequence](https://takeuforward.org/data-structure/find-k-th-permutation-sequence/)
- **Difficulty**: Hard
- **Statement**: Given $n$ and $k$, return the $k^{\text{th}}$ permutation sequence of numbers $1$ to $n$ in $\mathcal{O}(n^2)$ time without generating all $n!$ permutations.

---

## 1. Problem, Restated

Direct mathematical derivation using factorial bucket blocks: $(k / (n-1)!)$ determines digit choice.

- **Input**: Grid / String / Constraints.
- **Output**: Boolean feasibility or complete vector of solution configurations.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

There are $n!$ total permutations. The first digit fixes $(n-1)!$ permutations per choice. Convert $k$ to 0-indexed ($k = k - 1$). The first digit index is $k / (n-1)!$. Select `numbers[index]`, remove it, update $k = k \% (n-1)!$, and repeat for the next position.

- **Underlying Pattern**: `Factorial Number System (Factoradic Bucket Placement)`.

---

## 3. Approach 1 — Naive / Unpruned Search

### Idea
Generate all $N!$ permutations with `std::next_permutation` in $\mathcal{O}(N! \cdot N)$ time.

### C++17 Code
```cpp
#include <vector>
#include <string>
#include <numeric>
#include <algorithm>
using namespace std;
string getPermutationBrute(int n, int k) {
    string s = "";
    for (int i = 1; i <= n; i++) s += to_string(i);
    for (int i = 1; i < k; i++) next_permutation(s.begin(), s.end());
    return s;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(K \cdot N)$ time — for $N = 9$, $N! = 362880$ ops.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Generates unneeded intermediate permutations.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard backtracking algorithm below directly provides optimal pruning.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
Mathematical Factorial Bucket Selection in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
#include <vector>
#include <string>
using namespace std;

class Solution {
public:
    string getPermutation(int n, int k) {
        int fact = 1;
        vector<int> numbers;
        
        // Compute (n-1)! and fill numbers vector [1, 2, ..., n]
        for (int i = 1; i < n; i++) {
            fact *= i;
            numbers.push_back(i);
        }
        numbers.push_back(n);
        
        string ans = "";
        k = k - 1; // convert to 0-indexed
        
        while (true) {
            int index = k / fact;
            ans += to_string(numbers[index]);
            numbers.erase(numbers.begin() + index); // O(N) erase
            
            if (numbers.empty()) break;
            
            k = k % fact;
            fact = fact / numbers.size();
        }
        
        return ans;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time ($N$ digits $\times N$ vector erase).
- **Space Complexity**: $\mathcal{O}(N)$ auxiliary space for numbers list.
- **Why this is optimal**: Calculates each digit in $\mathcal{O}(1)$ math without generating permutations.

---

## 6. Dry Run

$n = 4, k = 17$ ($k = 16$ 0-indexed, $fact = 3! = 6$)

| Step | Action / State Change | Result |
|---|---|---|
| `Digit 1` | index = 16 / 6 = 2 -> numbers[2] = 3. k = 16 % 6 = 4 | ans = "3", rem = [1, 2, 4], fact = 6/3=2 |
| `Digit 2` | index = 4 / 2 = 2 -> numbers[2] = 4. k = 4 % 2 = 0 | ans = "34", rem = [1, 2], fact = 2/2=1 |
| `Digit 3` | index = 0 / 1 = 0 -> numbers[0] = 1. k = 0 % 1 = 0 | ans = "341", rem = [2] |
| `Digit 4` | index = 0 -> numbers[0] = 2 | Final ans = `"3412"` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $k = 1$ (first permutation `"1234"`).
- $k = n!$ (last permutation `"4321"`).

### Common Bugs to Avoid
- Forgetting `k = k - 1` (breaks 0-indexed modulo arithmetic).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why convert k to 0-indexed upfront?**  
  **A**: Because $k / (n-1)!$ accurately maps to array index $[0, n-1]$ only when $k$ starts from 0 (e.g. for $k=6, fact=6$: $6/6=1$ gives index 1, but $5/6=0$ gives correct block 0).


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Maths`, `Factorial`, `LeetCode-60`, `Hard`
- **Related problems to practice next**:
- **Next Permutation**: Permutation algorithms.
