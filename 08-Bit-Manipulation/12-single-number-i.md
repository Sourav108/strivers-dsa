# Find the Only Odd Appearing Number (Single Number I) (Step 8.2 — Interview Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Find the Only Odd Appearing Number (Single Number I)](https://takeuforward.org/arrays/find-the-number-that-appears-once-and-the-other-numbers-twice/)
- **Difficulty**: Easy
- **Statement**: Given a non-empty array of integers `nums`, every element appears twice except for one. Find that single one in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ extra space.

---

## 1. Problem, Restated

XOR all elements in array: identical pairs cancel to 0, leaving the unique element.

- **Input**: Parameters specified.
- **Output**: Resulting bitwise integer / boolean / list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

XOR is associative and commutative: $a \oplus b \oplus a = (a \oplus a) \oplus b = 0 \oplus b = b$. By XOR-ing all elements together in a single pass, every number appearing twice cancels itself out to 0, leaving strictly the single unique element in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

- **Underlying Pattern**: `XOR Cumulative Prefix Cancellation ($\oplus_{i=1}^N x_i$)`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Hash Map counting frequencies in $\mathcal{O}(N)$ time and $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <unordered_map>
#include <vector>
using namespace std;
int singleNumberHash(vector<int>& nums) {
    unordered_map<int, int> mp;
    for (int x : nums) mp[x]++;
    for (auto& [x, count] : mp) if (count == 1) return x;
    return -1;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ hash map space.
- **Why it's not good enough**: Fails the $\mathcal{O}(1)$ memory requirement.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — direct $\mathcal{O}(1)$ bitwise operation below is optimal.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Single Pass XOR Accumulator in $\mathcal{O}(1)$ auxiliary space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int xorSum = 0;
        for (int num : nums) {
            xorSum ^= num;
        }
        return xorSum;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Pair cancellation requires zero memory allocations.

---

## 6. Dry Run

`nums = [4, 1, 2, 1, 2]`

| Step | Action / State Change | Result |
|---|---|---|
| `Order rearranged` | $4 \oplus (1 \oplus 1) \oplus (2 \oplus 2)$ | Associative grouping |
| `Cancellation` | $4 \oplus 0 \oplus 0 = 4$ | Result = 4 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single element array `[1]` (returns 1).
- Negative numbers (XOR handles signed integers seamlessly).

### Common Bugs to Avoid
- Initializing `xorSum = 1` instead of `0`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Can XOR be used if other elements appear 3 times instead of 2?**  
  **A**: No, for elements appearing 3 times, $x \oplus x \oplus x = x$ (does not cancel). For 3 appearances, use bitwise modulo-3 counting (LeetCode 137: Single Number II).


---

## 9. Tags & Related Problems

- **Tags**: `Bit Manipulation`, `XOR`, `LeetCode-136`, `Easy`
- **Related problems to practice next**:
- **Single Number III**: Two unique elements.
