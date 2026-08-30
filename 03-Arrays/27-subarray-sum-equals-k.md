# Count Subarrays with Sum Equals K (Step 3.2)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: https://takeuforward.org/arrays/count-subarray-sum-equals-k/
- **Difficulty**: Medium
- **Statement**: Find total number of continuous subarrays whose sum equals $k$.

---

## 1. Problem, Restated

Find total number of continuous subarrays whose sum equals $k$.

- **Input**: Vector of integers `nums`.
- **Output**: Result as specified by problem requirements.
- **Key Constraints**: $n$ up to $10^5$, elements can be negative/positive, time limit 1.0s.

---

## 2. Intuition & Pattern

Prefix Sum Frequency Map (`prefixSum - k` seen count).

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

int subarraySumBrute(const vector<int>& nums, int k) {
    int cnt = 0, n = nums.size();
    for (int i = 0; i < n; i++) {
        int s = 0;
        for (int j = i; j < n; j++) { s += nums[j]; if (s == k) cnt++; }
    }
    return cnt;
}
```

### Java Code
```java
class Solution {
    int subarraySumBrute(int[] nums, int k) {
        int cnt = 0, n = nums.length;
        for (int i = 0; i < n; i++) {
            int s = 0;
            for (int j = i; j < n; j++) { s += nums[j]; if (s == k) cnt++; }
        }
        return cnt;
    }
}
```

### Complexity Derivation
- **Time Complexity**: O(n^2)
- **Space Complexity**: O(1)
- **Why it's not good enough**: Evaluating all $n(n+1)/2$ subarrays takes $\mathcal{O}(n^2)$ time, causing TLE at $n = 2 \times 10^4$.

---

## 4. Approach 2 — Better

### Idea
Two Nested Loops with Running Sum: Check every subarray $(i, j)$ using a running sum accumulator and increment count whenever `sum == k`.

### C++17 Code
```cpp
#include <vector>
using namespace std;

int subarraySumBetter(const vector<int>& nums, int k) {
    int count = 0, n = nums.size();
    for (int i = 0; i < n; i++) {
        int sum = 0;
        for (int j = i; j < n; j++) {
            sum += nums[j];
            if (sum == k) count++;
        }
    }
    return count;
}
```

### Java Code
```java
class Solution {
    int subarraySumBetter(int[] nums, int k) {
        int count = 0, n = nums.length;
        for (int i = 0; i < n; i++) {
            int sum = 0;
            for (int j = i; j < n; j++) {
                sum += nums[j];
                if (sum == k) count++;
            }
        }
        return count;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n^2)$ — evaluates all $n(n+1)/2$ subarrays.
- **Space Complexity**: $\mathcal{O}(1)$ — constant memory.
- **Why it's still not optimal**: Quadratic time complexity causes TLE for $n = 2 \times 10^4$. Hash map prefix sum frequency reduces this to $\mathcal{O}(n)$.

---

## 5. Approach 3 — Optimal

### Idea
Prefix Sum Frequency Map: Maintain `unordered_map<int, int> prefixFreq` with `prefixFreq[0] = 1`. Accumulate `sum += x`. If `sum - k` is in map, add `prefixFreq[sum - k]` to total count. Increment `prefixFreq[sum]++`.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <climits>
#include <unordered_map>
#include <unordered_set>
using namespace std;

int subarraySumOptimal(const vector<int>& nums, int k) {
    unordered_map<int, int> prefixFreq;
    prefixFreq[0] = 1; // base case: empty prefix
    int sum = 0, count = 0;
    for (int x : nums) {
        sum += x;
        int rem = sum - k;
        if (prefixFreq.count(rem)) count += prefixFreq[rem];
        prefixFreq[sum]++;
    }
    return count;
}
```

### Java Code
```java
import java.util.*;

class Solution {
    int subarraySumOptimal(int[] nums, int k) {
        Map<Integer, Integer> prefixFreq = new HashMap<>();
        prefixFreq[0] = 1; // base case: empty prefix
        int sum = 0, count = 0;
        for (int x : nums) {
            sum += x;
            int rem = sum - k;
            if (prefixFreq.contains(rem)) count += prefixFreq[rem];
            prefixFreq[sum]++;
        }
        return count;
    }
}
```

### Complexity Derivation
- **Time Complexity**: O(n)
- **Space Complexity**: O(n)
- **Why this is optimal**: Counts valid subarrays in single-pass $\mathcal{O}(n)$ time and $\mathcal{O}(n)$ space.

---

## 6. Dry Run

`nums = [1, 1, 1]`, `k = 2`

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | sum=0, count=0, prefixFreq={0: 1} | ready |
| `i=0 (x=1)` | sum=1, rem=1-2=-1, not in map -> prefixFreq[1]=1 | count=0 |
| `i=1 (x=1)` | sum=2, rem=2-2=0 in map (freq 1) -> count += 1 -> count=1, prefixFreq[2]=1 | count=1 |
| `i=2 (x=1)` | sum=3, rem=3-2=1 in map (freq 1) -> count += 1 -> count=2, prefixFreq[3]=1 | count=2 |
| `Result` | - | Total count of subarrays with sum 2 is **2** (`[nums[0..1], nums[1..2]]`) |

## 7. Edge Cases & Common Bugs

### Edge Cases
- Negative numbers cancelling sum (`[1, -1, 0], k=0` -> correctly counts subarrays).
- Subarrays starting from index 0 -> enabled by `prefixFreq[0] = 1`.

### Common Bugs to Avoid
- Forgetting `prefixFreq[0] = 1` base initialization.
- Using sliding window on arrays with negative values.

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why must we initialize `prefixFreq[0] = 1` in the hash map?**  
  **A**: Because if a prefix sum equals $k$ exactly at index $i$ (`sum == k`), then `sum - k = 0`. The count `prefixFreq[0] = 1` credits the valid subarray extending from index 0 to index $i$.

- **Q2: Why does the Sliding Window approach fail when negative numbers are present?**  
  **A**: Sliding window requires monotonicity: expanding the window must strictly increase the sum, and shrinking must decrease it. Negative numbers destroy monotonicity, so a hash map is required.

- **Q3: How to find the number of subarrays whose sum is DIVISIBLE by K (LeetCode 974)?**  
  **A**: Use modulo arithmetic: store frequencies of normalized remainders `rem = (sum % k + k) % k`. If `prefixMap.count(rem)`, add count to total in $\mathcal{O}(n)$ time.

- **Q4: What if the array is purely positive and we want all subarrays summing to K?**  
  **A**: Two Pointers sliding window finds count in $\mathcal{O}(n)$ time and $\mathcal{O}(1)$ space by maintaining running sum and adjusting window edges.

- **Q5: How to parallelize counting subarrays summing to K?**  
  **A**: Compute parallel prefix sums across chunks, construct local prefix frequency maps, and cross-correlate prefix sums between chunk boundaries.

## 9. Tags & Related Problems

- **Tags**: `Array`, `TakeUForward`, `Strivers-A2Z`, `Medium`
- **Related problems**:
  - Similar Step 3 Array Problems in the curriculum.
