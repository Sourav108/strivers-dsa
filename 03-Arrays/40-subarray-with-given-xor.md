# Subarrays with Given XOR (Advanced) (Step 3.3)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: https://takeuforward.org/data-structure/count-the-number-of-subarrays-with-given-xor-k/
- **Difficulty**: Hard
- **Statement**: Count subarrays having XOR equal to target integer $k$.

---

## 1. Problem, Restated

Count subarrays having XOR equal to target integer $k$.

- **Input**: Vector of integers `nums`.
- **Output**: Result as specified by problem requirements.
- **Key Constraints**: $n$ up to $10^5$, elements can be negative/positive, time limit 1.0s.

---

## 2. Intuition & Pattern

Prefix XOR Hashing (Mathematical invariant $Y = XR \oplus K$).

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

int countXORSubarraysBrute(const vector<int>& nums, int k) {
    int cnt = 0, n = nums.size();
    for (int i = 0; i < n; i++) {
        int x = 0;
        for (int j = i; j < n; j++) { x ^= nums[j]; if (x == k) cnt++; }
    }
    return cnt;
}
```

### Complexity Derivation
- **Time Complexity**: O(n^2)
- **Space Complexity**: O(1)
- **Why it's not good enough**: Evaluating XOR of all subarrays with nested loops takes $\mathcal{O}(n^2)$ time.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal

### Idea
Prefix XOR Hash Map: Maintain `xr = 0` and `xorFreq[0] = 1`. For each $x$: `xr ^= x`. If `xr ^ k` is in map, add `xorFreq[xr ^ k]` to count. Increment `xorFreq[xr]++`.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <climits>
#include <unordered_map>
#include <unordered_set>
using namespace std;

int countXORSubarraysOptimal(const vector<int>& nums, int k) {
    unordered_map<int, int> xorFreq;
    xorFreq[0] = 1;
    int xr = 0, count = 0;
    for (int x : nums) {
        xr ^= x;
        int rem = xr ^ k;
        if (xorFreq.count(rem)) count += xorFreq[rem];
        xorFreq[xr]++;
    }
    return count;
}
```

### Complexity Derivation
- **Time Complexity**: O(n)
- **Space Complexity**: O(n)
- **Why this is optimal**: Computes exact subarray count in single-pass $\mathcal{O}(n)$ time and $\mathcal{O}(n)$ space.

---

## 6. Dry Run

`nums = [4, 2, 2, 6, 4]`, `k = 6`

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | xr=0, freq={0:1}, count=0 | ready |
| `i=0 (x=4)` | xr=4, target=4^6=2 not in map -> freq[4]=1 | count=0 |
| `i=1 (x=2)` | xr=6, target=6^6=0 in map (freq 1) -> count += 1 -> count=1, freq[6]=1 | count=1 |
| `i=2 (x=2)` | xr=4, target=4^6=2 not in map -> freq[4]=2 | count=1 |
| `i=3 (x=6)` | xr=2, target=2^6=4 in map (freq 2) -> count += 2 -> count=3, freq[2]=1 | count=3 |
| `i=4 (x=4)` | xr=6, target=6^6=0 in map (freq 1) -> count += 1 -> count=4 | Final count: 4 ✅ |

## 7. Edge Cases & Common Bugs

### Edge Cases
- k = 0 -> counts subarrays where elements cancel to 0.
- Single element array matching k -> count = 1.

### Common Bugs to Avoid
- Omitting `xorFreq[0] = 1` base case.

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is the mathematical proof of the prefix XOR frequency relation?**  
  **A**: Let $XR_i$ be prefix XOR from index 0 to $i$. Subarray $nums[j..i]$ has XOR $k \iff XR_i \oplus XR_{j-1} = k$. XORing both sides with $k \oplus XR_{j-1}$ yields $XR_{j-1} = XR_i \oplus k$. Thus, every past occurrence of prefix XOR $(XR_i \oplus k)$ forms a valid subarray.

- **Q2: Why is `xorFreq[0] = 1` initialized in the frequency map?**  
  **A**: If the prefix XOR from index 0 to $i$ equals $k$ ($XR_i = k$), then $XR_i \oplus k = 0$. The base count `xorFreq[0] = 1` accounts for subarrays starting at index 0.

- **Q3: How does this compare with Count Subarrays with Sum Equals K?**  
  **A**: They are mathematically isomorphic: Subarray Sum uses subtraction ($S - k$), Subarray XOR uses bitwise XOR ($XR \oplus k$).

- **Q4: How to find the LONGEST subarray with XOR equal to K?**  
  **A**: Instead of storing frequency count, store the *first seen index* in `unordered_map<int, int> firstSeen`. Update `maxLen = max(maxLen, i - firstSeen[XR ^ k])`.

- **Q5: How to solve Maximum XOR Subarray in O(n) using a Bitwise Trie?**  
  **A**: Insert each prefix XOR into a binary Trie (depth 32). For each prefix XOR, query the Trie for the path that maximizes opposite bits at each position from MSB to LSB in $\mathcal{O}(32 \cdot n) = \mathcal{O}(n)$ time.

## 9. Tags & Related Problems

- **Tags**: `Array`, `TakeUForward`, `Strivers-A2Z`, `Hard`
- **Related problems**:
  - Similar Step 3 Array Problems in the curriculum.
