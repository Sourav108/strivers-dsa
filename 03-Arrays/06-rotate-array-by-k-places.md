# Rotate Array by K Places (Step 3.1)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: https://takeuforward.org/data-structure/rotate-array-by-k-elements/
- **Difficulty**: Medium
- **Statement**: Rotate an array to the right by $k$ steps ($k \ge 0$).

---

## 1. Problem, Restated

Rotate an array to the right by $k$ steps ($k \ge 0$).

- **Input**: Vector of integers `nums`.
- **Output**: Result as specified by problem requirements.
- **Key Constraints**: $n$ up to $10^5$, elements can be negative/positive, time limit 1.0s.

---

## 2. Intuition & Pattern

Reversal Algorithm: Reverse whole array, reverse first k, reverse remaining n-k.

- **Underlying Pattern**: Array Manipulation / Mathematical Invariants / Pointers.
- **The "Aha!" Moment**: Recognizing how to avoid redundant work by storing running state or leveraging sorting invariants.

---

## 3. Approach 1 — Brute Force

### Idea
Check all possibilities exhaustively using nested loops.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <climits>
#include <set>
#include <unordered_map>
using namespace std;

void rotateBrute(vector<int>& nums, int k) {
    int n = nums.size(); k %= n;
    vector<int> temp(n);
    for (int i = 0; i < n; i++) temp[(i + k) % n] = nums[i];
    nums = temp;
}
```

### Complexity Derivation
- **Time Complexity**: O(n)
- **Space Complexity**: O(n)
- **Why it's not good enough**: Shifting by 1 repeated $k$ times takes $\mathcal{O}(n \cdot k)$ operations, which is too slow when $k \approx n$.

---

## 4. Approach 2 — Better

### Idea
Temporary Array: Store the last $k$ elements in an auxiliary vector `temp`. Shift the first $n-k$ elements to the right by $k$ positions, then copy `temp` into the first $k$ slots of the original array.

### C++17 Code
```cpp
#include <vector>
using namespace std;

void rotateBetter(vector<int>& nums, int k) {
    int n = nums.size();
    k %= n;
    if (k == 0) return;
    
    // Copy last k elements
    vector<int> temp(k);
    for (int i = 0; i < k; i++) {
        temp[i] = nums[n - k + i];
    }
    // Shift remaining elements rightward
    for (int i = n - k - 1; i >= 0; i--) {
        nums[i + k] = nums[i];
    }
    // Copy temp to front
    for (int i = 0; i < k; i++) {
        nums[i] = temp[i];
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n)$ — copies $k$ elements, shifts $n-k$ elements, and copies $k$ elements back.
- **Space Complexity**: $\mathcal{O}(k)$ — auxiliary space for temporary buffer.
- **Why it's still not optimal**: Consumes $\mathcal{O}(k)$ extra memory; the 3-step reversal algorithm achieves the same $\mathcal{O}(n)$ time in strictly $\mathcal{O}(1)$ space.

---

## 5. Approach 3 — Optimal

### Idea
3-Step Reversal Algorithm: Normalize $k = k \% n$. 1) Reverse the entire array `nums[0..n-1]`. 2) Reverse the first $k$ elements `nums[0..k-1]`. 3) Reverse the remaining $n-k$ elements `nums[k..n-1]`.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <climits>
#include <unordered_map>
#include <unordered_set>
using namespace std;

void rotateOptimal(vector<int>& nums, int k) {
    int n = nums.size();
    k %= n;
    reverse(nums.begin(), nums.end());
    reverse(nums.begin(), nums.begin() + k);
    reverse(nums.begin() + k, nums.end());
}
```

### Complexity Derivation
- **Time Complexity**: O(n)
- **Space Complexity**: O(1)
- **Why this is optimal**: Rotates all elements in exactly $2n$ element swaps ($\mathcal{O}(n)$ time) and strictly $\mathcal{O}(1)$ auxiliary space.

---

## 6. Dry Run

`nums = [1, 2, 3, 4, 5, 6, 7]`, `k = 3`

| Step | Action / State Change | Result |
|---|---|---|
| `Step 1` | Reverse entire array | `[7, 6, 5, 4, 3, 2, 1]` |
| `Step 2` | Reverse first k=3 elements | `[5, 6, 7, 4, 3, 2, 1]` |
| `Step 3` | Reverse remaining 4 elements | `[5, 6, 7, 1, 2, 3, 4]` ✅ |

## 7. Edge Cases & Common Bugs

### Edge Cases
- k is multiple of n (`k = n` -> array unchanged).
- k is larger than n (`k = 10, n = 7` -> normalized to $k = 3$).
- Single element array (`nums = [1], k = 3` -> unchanged).

### Common Bugs to Avoid
- Forgetting `k %= n`, causing out-of-bounds iterators when $k > n$.
- Reversing wrong subranges.

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does the 3-Step Reversal Algorithm work mathematically?**  
  **A**: Let the array be split into two parts $A = \text{nums}[0..n-k-1]$ and $B = \text{nums}[n-k..n-1]$. We want the result $BA$. Reversing $A$ gives $A^R$, reversing $B$ gives $B^R$, giving $A^R B^R$. Reversing the whole array gives $(A^R B^R)^R = (B^R)^R (A^R)^R = BA$.

- **Q2: What is the Juggling Algorithm (Block Swap) approach using GCD(n, k)?**  
  **A**: The array elements form $\gcd(n, k)$ independent cyclic permutation sets. We traverse each cycle by jumping $k$ steps at a time with a temporary variable, performing exactly $n$ moves in $\mathcal{O}(n)$ time and $\mathcal{O}(1)$ space.

- **Q3: What happens when k is larger than n or negative?**  
  **A**: Normalize $k = (k \% n + n) \% n$. This handles $k > n$ and converts negative rotations (left rotations) into equivalent positive right rotations.

- **Q4: Can array rotation be done in O(1) time in system design?**  
  **A**: Yes, in a Circular Buffer / Ring Buffer data structure, rotation is $\mathcal{O}(1)$ by simply shifting the `headIndex = (headIndex + k) % n` pointer without moving any elements in memory.

- **Q5: Can we parallelize the reversal algorithm?**  
  **A**: Yes, each of the three `std::reverse` operations is embarrassingly parallel: independent CPU threads swap symmetric index pairs `(start + i, end - i)` concurrently.

## 9. Tags & Related Problems

- **Tags**: `Array`, `TakeUForward`, `Strivers-A2Z`, `Medium`
- **Related problems**:
  - Similar Step 3 Array Problems in the curriculum.
