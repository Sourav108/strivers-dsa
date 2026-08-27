# Count Subarrays with Given XOR K (Step 3.3)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: https://takeuforward.org/data-structure/count-the-number-of-subarrays-with-given-xor-k/
- **Difficulty**: Medium
- **Statement**: Find number of subarrays having bitwise XOR equal to $K$.

---

## 1. Problem, Restated

Find number of subarrays having bitwise XOR equal to $K$.

- **Input**: Vector of integers `nums`.
- **Output**: Result as specified by problem requirements.
- **Key Constraints**: $n$ up to $10^5$, elements can be negative/positive, time limit 1.0s.

---

## 2. Intuition & Pattern

Prefix XOR Frequency Map: if $XR \oplus Y = K$, then $Y = XR \oplus K$.

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

int subarraysWithXorKBrute(const vector<int>& nums, int k) {
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
- **Why it's not good enough**: Evaluating all subarrays with nested loops takes $\mathcal{O}(n^2)$ time.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal

### Idea
Prefix XOR Frequency Map: Maintain `xr = 0` and `freq[0] = 1`. For each $x$: compute `xr ^= x`, query `target = xr ^ k` in `freq`, add `freq[target]` to `count`, and increment `freq[xr]++`.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <climits>
#include <unordered_map>
#include <unordered_set>
using namespace std;

int subarraysWithXorKOptimal(const vector<int>& nums, int k) {
    unordered_map<int, int> freq;
    freq[0] = 1;
    int xr = 0, count = 0;
    for (int x : nums) {
        xr ^= x;
        int target = xr ^ k;
        if (freq.count(target)) count += freq[target];
        freq[xr]++;
    }
    return count;
}
```

### Complexity Derivation
- **Time Complexity**: O(n)
- **Space Complexity**: O(n)
- **Why this is optimal**: Isomorphic to Subarray Sum Equals K, solving the XOR formulation in $\mathcal{O}(n)$ time and $\mathcal{O}(n)$ space.

---

## 6. Dry Run

`nums = [4, 2, 2, 6, 4]`, `k = 6`

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | xr=0, freq={0: 1}, count=0 | ready |
| `i=0 (x=4)` | xr=4, target=4^6=2, not in map -> freq[4]=1 | count=0 |
| `i=1 (x=2)` | xr=6, target=6^6=0 in map (freq 1) -> count += 1 -> count=1, freq[6]=1 | count=1 |
| `i=2 (x=2)` | xr=4, target=4^6=2, not in map -> freq[4]=2 | count=1 |
| `i=3 (x=6)` | xr=2, target=2^6=4 in map (freq 2) -> count += 2 -> count=3, freq[2]=1 | count=3 |
| `i=4 (x=4)` | xr=6, target=6^6=0 in map (freq 1) -> count += 1 -> count=4 | Final count: 4 ✅ |

## 7. Edge Cases & Common Bugs

### Edge Cases
- k = 0 -> counts subarrays with XOR 0 (elements cancel).
- Single element array matching k -> count = 1.

### Common Bugs to Avoid
- Missing `freq[0] = 1` base initialization.

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is the mathematical proof of the prefix XOR frequency map?**  
  **A**: Let $XR_i$ be prefix XOR up to $i$. Subarray $nums[j..i]$ has XOR $k \iff XR_i \oplus XR_{j-1} = k$. XORing both sides with $k \oplus XR_{j-1}$ gives $XR_{j-1} = XR_i \oplus k$. Every time $XR_i \oplus k$ has occurred in past prefix XORs, it forms a valid subarray.

- **Q2: Why is `freq[0] = 1` initialized in the frequency map?**  
  **A**: If prefix XOR $XR_i == k$, then $XR_i \oplus k = 0$. The entry `freq[0] = 1` accounts for the full prefix subarray from index 0 to $i$ having XOR equal to $k$.

- **Q3: How does this compare with Count Subarrays with Sum K?**  
  **A**: The algorithms are mathematically isomorphic: Subarray Sum uses $(S - k)$, Subarray XOR uses $(XR \oplus k)$ because XOR is its own self-inverse ($x \oplus x = 0$).

- **Q4: Can we implement this using a Bitwise Trie instead of a hash map?**  
  **A**: Yes, insert 32-bit prefix XORs into a Trie where each node stores subtree insertion counts. Querying $XR \oplus k$ takes $\mathcal{O}(32) = \mathcal{O}(1)$ deterministic time without hash collisions.

- **Q5: How to solve Maximum XOR Subarray (finding subarray with maximum possible XOR)?**  
  **A**: Maintain prefix XORs in a Bitwise Trie. For each prefix XOR, query the Trie for the path that maximizes opposite bits at each step in $\mathcal{O}(32 \cdot n) = \mathcal{O}(n)$ time.

## 9. Tags & Related Problems

- **Tags**: `Array`, `TakeUForward`, `Strivers-A2Z`, `Medium`
- **Related problems**:
  - Similar Step 3 Array Problems in the curriculum.
