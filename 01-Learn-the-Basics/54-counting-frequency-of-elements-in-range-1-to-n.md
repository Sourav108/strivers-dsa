# Counting Frequency of Elements in Range 1 to N (O(1) Space) (Step 1.6 — Learn Basic Hashing)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Counting Frequency of Elements in Range 1 to N (O(1) Space)](https://takeuforward.org/data-structure/frequency-of-elements/)
- **Difficulty**: Easy
- **Statement**: Given an array `arr` of $n$ positive integers where each integer is in the range $[1, n]$, count the frequency of all elements from $1$ to $n$ in $\mathcal{O}(n)$ time and $\mathcal{O}(1)$ extra space (modifying the array in-place).

---

## 1. Problem, Restated

Count frequencies of elements $1 \dots n$ in an array of size $n$ using the array itself as the hash map via mathematical encoding.

- **Input**: Parameters specified.
- **Output**: Value or side-effect meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Since all elements lie in $[1, n]$ and array indices are $[0, n-1]$, each index $i$ corresponds to the number $i + 1$. We can encode two numbers in a single array cell using modulo arithmetic: `val = original_val + count * (n + 1)`. To extract original value: `val % (n + 1)`. To extract frequency count: `val / (n + 1)`. Runs in $\mathcal{O}(n)$ time and strictly $\mathcal{O}(1)$ extra memory!

- **Underlying Pattern**: `In-Place Array Hash Encoding ($A[i] = A[i] + (\text{freq} \% K) \times K$)`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Standard frequency array `vector<int> freq(n+1, 0)` in $\mathcal{O}(n)$ space.

### C++17 Code
```cpp
#include <vector>
using namespace std;
vector<int> countFreqExtraSpace(vector<int>& arr) {
    int n = arr.size();
    vector<int> freq(n, 0);
    for (int x : arr) {
        if (x >= 1 && x <= n) freq[x - 1]++;
    }
    return freq;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n)$ time.
- **Space Complexity**: $\mathcal{O}(n)$ auxiliary memory.
- **Why it's not good enough**: Fails the strict $\mathcal{O}(1)$ auxiliary space constraint.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
In-Place Modulo Hash Encoding: add `(n + 1)` to the target index for each occurrence.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
public:
    // Function to count the frequency of all elements from 1 to N in-place
    void frequencyCount(vector<int>& arr, int N, int P) {
        int K = N + 1; // Base multiplier
        
        // Pass 1: Encode frequencies using modulo arithmetic
        for (int i = 0; i < N; i++) {
            // Extract the original element value before any modifications
            int originalVal = arr[i] % K;
            
            // If the element is within range [1, N], increment the count at index (originalVal - 1)
            if (originalVal >= 1 && originalVal <= N) {
                arr[originalVal - 1] += K;
            }
        }
        
        // Pass 2: Extract frequency counts (quotient of division by K)
        for (int i = 0; i < N; i++) {
            arr[i] = arr[i] / K;
        }
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n)$ — two linear passes over the array.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space — modifies array in-place without allocating memory.
- **Why this is optimal**: Mathematical modulo encoding packs both historical and frequency values into 32-bit integer cells.

---

## 6. Dry Run

`arr = [2, 3, 3, 2, 5]`, $N = 5, K = 6$

| Step | Action / State Change | Result |
|---|---|---|
| `i = 0` | orig = arr[0]%6 = 2 -> target index = 1. arr[1] += 6 (3 + 6 = 9) | `[2, 9, 3, 2, 5]` |
| `i = 1` | orig = arr[1]%6 = 3 -> target index = 2. arr[2] += 6 (3 + 6 = 9) | `[2, 9, 9, 2, 5]` |
| `i = 2` | orig = arr[2]%6 = 3 -> target index = 2. arr[2] += 6 (9 + 6 = 15) | `[2, 9, 15, 2, 5]` |
| `i = 3` | orig = arr[3]%6 = 2 -> target index = 1. arr[1] += 6 (9 + 6 = 15) | `[2, 15, 15, 2, 5]` |
| `i = 4` | orig = arr[4]%6 = 5 -> target index = 4. arr[4] += 6 (5 + 6 = 11) | `[2, 15, 15, 2, 11]` |
| `Pass 2` | Divide all cells by 6: `arr[i] / 6` | `[0, 2, 2, 0, 1]` ✅ (1:0, 2:2, 3:2, 4:0, 5:1) |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Elements $> N$ in array (ignored by range check $1 \le \text{orig} \le N$).
- Array with all identical elements (`[1, 1, 1, 1]`).

### Common Bugs to Avoid
- Using `arr[i]` directly without `% K` in the loop (reads already-modified values).
- Using $K = N$ instead of $K = N + 1$ (if original value is $N$, $N \% N = 0$ corrupts the extraction).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is K chosen as N + 1 instead of N?**  
  **A**: Because elements range from $1$ to $N$. If $K = N$, an element with value $N$ evaluates to $N \% N = 0$, destroying the original value. Choosing $K = N + 1$ guarantees that all values in $[1, N]$ satisfy $\text{val} < K$, so $\text{val} \% K = \text{val}$ unconditionally!

- **Q2: What is the maximum value a cell can reach in this encoding?**  
  **A**: If all $N$ elements are identical (e.g. all 1s), index 0 will be incremented $N$ times: $\text{finalVal} = \text{original} + N \times (N + 1) \approx N^2$. For $N = 10^5$, $N^2 = 10^{10}$, which exceeds 32-bit signed integer. For large $N$, use `long long` cast or the 1-based negation index marking technique.


---

## 9. Tags & Related Problems

- **Tags**: `Hashing`, `Arrays`, `In-Place`, `Maths`, `Easy`
- **Related problems to practice next**:
- **Counting Frequencies**: Standard hash map frequency.
- **Find Missing and Repeating Number**: In-place array hashing.
