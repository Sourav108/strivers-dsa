# Find the Two Numbers Occurring Odd Number of Times (Single Number III) (Step 8.2 — Interview Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Find the Two Numbers Occurring Odd Number of Times (Single Number III)](https://takeuforward.org/bit-manipulation/two-numbers-with-odd-occurrences/)
- **Difficulty**: Medium
- **Statement**: Given an integer array `nums` in which exactly two elements appear only once and all other elements appear exactly twice, find the two elements that appear only once in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

---

## 1. Problem, Restated

Separate two unique numbers by partitioning array elements based on their rightmost differing set bit.

- **Input**: Parameters specified.
- **Output**: Resulting bitwise integer / boolean / list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) XOR all numbers: `xorSum = a ^ b` (all duplicate pairs cancel). 2) Since $a \ne b$, `xorSum != 0`, meaning $a$ and $b$ must differ in at least one bit! 3) Find the lowest set bit in `xorSum`: `rightmost = xorSum & -xorSum` (or `(xorSum & (xorSum - 1)) ^ xorSum` for overflow safety). 4) Partition the array into two buckets: Bucket 1 (elements with this bit set) and Bucket 2 (elements with this bit unset). XOR-ing each bucket separately cancels duplicate pairs and isolates $a$ and $b$!

- **Underlying Pattern**: `XOR Full XORSum + Rightmost Set Bit Partitioning (`rightmost = xorSum & -xorSum`)`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Hash Map frequency counting in $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <unordered_map>
#include <vector>
using namespace std;
vector<int> singleNumberHash(vector<int>& nums) {
    unordered_map<int, int> mp;
    for (int x : nums) mp[x]++;
    vector<int> ans;
    for (auto& [k, v] : mp) if (v == 1) ans.push_back(k);
    return ans;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ hash space.
- **Why it's not good enough**: Violates $\mathcal{O}(1)$ space constraint.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — direct $\mathcal{O}(1)$ bitwise operation below is optimal.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Rightmost Differing Bit Partitioning in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
public:
    vector<int> singleNumber(vector<int>& nums) {
        // Step 1: Compute full XOR sum = a ^ b
        long long xorSum = 0;
        for (int x : nums) {
            xorSum ^= x;
        }
        
        // Step 2: Isolate the rightmost set bit (where a and b differ)
        long long rightmostBit = xorSum & (-xorSum);
        
        // Step 3: Divide numbers into 2 buckets and XOR separately
        int b1 = 0, b2 = 0;
        for (int x : nums) {
            if (x & rightmostBit) {
                b1 ^= x; // bucket with bit set
            } else {
                b2 ^= x; // bucket with bit unset
            }
        }
        
        return {b1, b2};
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ two passes.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Differentiates the two unique numbers using their first distinct bit position.

---

## 6. Dry Run

`nums = [1, 2, 1, 3, 2, 5]` ($a = 3, b = 5$)

| Step | Action / State Change | Result |
|---|---|---|
| `Step 1 (XOR all)` | $3 \oplus 5 = 011_2 \oplus 101_2 = 110_2$ (6) | xorSum = 6 |
| `Step 2 (Rightmost bit)` | $6 \& -6 = 110 \& 010 = 010_2$ (2) | Bit 1 differs! |
| `Bucket 1 (bit 1 set)` | Includes `[2, 3, 2]` -> XOR sum = 3 | $a = 3$ isolated! |
| `Bucket 2 (bit 1 unset)` | Includes `[1, 1, 5]` -> XOR sum = 5 | $b = 5$ isolated! |
| `Result` | Both unique numbers extracted | `[3, 5]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `xorSum = INT_MIN` (negation `-xorSum` overflows 32-bit `int`, cast to `long long`).

### Common Bugs to Avoid
- Using signed 32-bit `int` for `xorSum & (-xorSum)` when `xorSum == INT_MIN`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does -xorSum isolate the lowest set bit?**  
  **A**: In Two's Complement arithmetic, $-x = \sim x + 1$. Inverting $x$ and adding 1 flips all bits up to the lowest set bit and keeps the lowest set bit as 1. AND-ing with $x$ isolates that single bit!


---

## 9. Tags & Related Problems

- **Tags**: `Bit Manipulation`, `XOR`, `LeetCode-260`, `Medium`
- **Related problems to practice next**:
- **Single Number I**: Single unique.
- **Find Missing and Repeating Number**: XOR partitioning.
