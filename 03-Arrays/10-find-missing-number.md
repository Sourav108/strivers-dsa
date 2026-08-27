# Find Missing Number in Array (Step 3.1)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: https://takeuforward.org/arrays/find-the-missing-number-in-an-array/
- **Difficulty**: Easy
- **Statement**: Given array of $n-1$ distinct numbers in range $[1, n]$, find the one missing number.

---

## 1. Problem, Restated

Given array of $n-1$ distinct numbers in range $[1, n]$, find the one missing number.

- **Input**: Vector of integers `nums`.
- **Output**: Result as specified by problem requirements.
- **Key Constraints**: $n$ up to $10^5$, elements can be negative/positive, time limit 1.0s.

---

## 2. Intuition & Pattern

Sum formula $S = \frac{n(n+1)}{2}$ or XOR cancellation ($x \oplus x = 0$).

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

int missingBrute(const vector<int>& nums, int n) {
    for (int i = 1; i <= n; i++) {
        bool found = false;
        for (int x : nums) if (x == i) { found = true; break; }
        if (!found) return i;
    }
    return -1;
}
```

### Complexity Derivation
- **Time Complexity**: O(n^2)
- **Space Complexity**: O(1)
- **Why it's not good enough**: Nested linear search for each number from 1 to $n$ takes $\mathcal{O}(n^2)$ time.

---

## 4. Approach 2 — Better

### Idea
Hash/Frequency Array: Create a boolean/frequency array of size $n+1$ initialized to 0. Mark `hash[x] = 1` for each number seen in `nums`. The index $i \in [1, n]$ where `hash[i] == 0` is the missing number.

### C++17 Code
```cpp
#include <vector>
using namespace std;

int findMissingBetter(const vector<int>& nums, int n) {
    vector<int> freq(n + 1, 0);
    for (int x : nums) {
        freq[x] = 1;
    }
    for (int i = 1; i <= n; i++) {
        if (freq[i] == 0) return i;
    }
    return -1;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2n) = \mathcal{O}(n)$ — two linear scans.
- **Space Complexity**: $\mathcal{O}(n)$ — frequency array of size $n+1$.
- **Why it's still not optimal**: Consumes $\mathcal{O}(n)$ auxiliary memory when bitwise XOR or Gauss sum can isolate the missing number in $\mathcal{O}(1)$ space.

---

## 5. Approach 3 — Optimal

### Idea
Bitwise XOR Cancellation: XOR all integers from 1 to $n$ with all elements in `nums`. Since $x \oplus x = 0$ and $x \oplus 0 = x$, every present number cancels out, leaving only the missing number.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <climits>
#include <unordered_map>
#include <unordered_set>
using namespace std;

int missingOptimal(const vector<int>& nums, int n) {
    int xorAll = 0;
    for (int i = 1; i <= n; i++) xorAll ^= i;
    for (int x : nums) xorAll ^= x;
    return xorAll;
}
```

### Complexity Derivation
- **Time Complexity**: O(n)
- **Space Complexity**: O(1)
- **Why this is optimal**: Computes the missing number in $\mathcal{O}(n)$ time and $\mathcal{O}(1)$ space while completely avoiding integer overflow.

---

## 6. Dry Run

`nums = [1, 2, 4, 5]`, `n = 5`

| Step | Action / State Change | Result |
|---|---|---|
| `Step 1` | XOR all 1..5: 1 ^ 2 ^ 3 ^ 4 ^ 5 = 1 | xorAll = 1 |
| `Step 2` | XOR all nums: 1 ^ 2 ^ 4 ^ 5 = 2 | xorNums = 2 |
| `Step 3` | xorAll ^ xorNums = (1 ^ 2 ^ 3 ^ 4 ^ 5) ^ (1 ^ 2 ^ 4 ^ 5) = 3 | Result: 3 ✅ |

## 7. Edge Cases & Common Bugs

### Edge Cases
- Missing number is 1 (`[2, 3, 4, 5]` -> returns 1).
- Missing number is n (`[1, 2, 3, 4]` -> returns 5).
- Single element array (`[2], n=2` -> returns 1).

### Common Bugs to Avoid
- Using $\frac{n(n+1)}{2}$ with 32-bit signed integers when $n = 10^5$, causing integer overflow.
- Loop bounds off-by-one (iterating up to $n-1$ instead of $n$).

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is the XOR approach preferred over the Gauss Sum formula n*(n+1)/2?**  
  **A**: For $n = 10^5$, $\frac{n(n+1)}{2} \approx 5 \times 10^9$, which overflows a standard 32-bit signed integer `int` and requires `long long`. The bitwise XOR approach never overflows any integer boundary.

- **Q2: What if TWO numbers are missing instead of one?**  
  **A**: Compute $X = \text{XOR of all } 1..n \oplus \text{XOR of array}$. $X = A \oplus B$. Find the rightmost set bit in $X$, partition numbers into two buckets based on that bit, and XOR each bucket to isolate $A$ and $B$ in $\mathcal{O}(n)$ time and $\mathcal{O}(1)$ space.

- **Q3: What if the array is already sorted?**  
  **A**: Use Binary Search in $\mathcal{O}(\log n)$ time: if `nums[mid] == mid + 1`, the left half is complete and the missing number is in the right half (`low = mid + 1`); otherwise `high = mid - 1`.

- **Q4: How does this adapt to a network stream with packet loss?**  
  **A**: Initialize `xorSum = 0`. XOR packet IDs as they are sent, and XOR received packet IDs. At the end of the batch, `xorSum` contains the exact dropped packet ID in $\mathcal{O}(1)$ extra memory.

- **Q5: What if numbers are in the range [0, n] (LeetCode 268)?**  
  **A**: XOR all integers from $0$ to $n$ against all elements in the array; the surviving value is the missing integer.

## 9. Tags & Related Problems

- **Tags**: `Array`, `TakeUForward`, `Strivers-A2Z`, `Easy`
- **Related problems**:
  - Similar Step 3 Array Problems in the curriculum.
