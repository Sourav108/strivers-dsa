# Minimum Days to Make M Bouquets (Step 4.2 — BS on Answers)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [LeetCode #1482 - Minimum Number of Days to Make m Bouquets](https://leetcode.com/problems/minimum-number-of-days-to-make-m-bouquets/) | [TakeUForward](https://takeuforward.org/binary-search/minimum-days-to-make-m-bouquets/)
- **Difficulty**: Medium
- **Statement**: You are given an integer array `bloomDay`, an integer $m$ and an integer $k$. You want to make $m$ bouquets. To make a bouquet, you need to use $k$ **adjacent** flowers from the garden. The garden consists of $n$ flowers, the $i$-th flower will bloom on day `bloomDay[i]` and then can be used in exactly one bouquet. Return the minimum number of days you need to wait to be able to make $m$ bouquets from the garden. If it is impossible to make $m$ bouquets, return `-1`.

---

## 1. Problem, Restated

Find the earliest day $D$ such that at least $m$ bouquets can be formed, where each bouquet requires $k$ contiguous, bloomed flowers.
- If the total flowers required ($m \times k$) exceeds the total number of flowers in the garden ($n$), it is physically impossible $\implies$ return `-1`.
- On any given day $D$, a flower at index $i$ is bloomed if and only if $\text{bloomDay}[i] \le D$.

- **Input**: Vector `bloomDay` of size $n$, integers $m$ (bouquets needed) and $k$ (adjacent flowers per bouquet).
- **Output**: Minimum integer day $D$, or `-1` if impossible.
- **Constraints**: $1 \le n \le 10^5$, $1 \le \text{bloomDay}[i] \le 10^9$, $1 \le m \le 10^6$, $1 \le k \le n$.

---

## 2. Intuition & Pattern

### Monotonicity of Garden Bloom
As time progresses (day $D$ increases), flowers that have bloomed stay bloomed forever. Therefore:
- The total number of bloomed flowers increases monotonically with $D$.
- The number of valid bouquets of $k$ adjacent flowers that can be formed **monotonically increases**.

The feasibility function $\text{canMake}(D) = (\text{bouquets formed on day } D \ge m)$ produces a monotonic boolean predicate across the day search space $[\min(\text{bloomDay}), \max(\text{bloomDay})]$:
- `[false, false, ..., false, true, true, ..., true]`

We want the **FIRST `true`** (the minimum day $D$).

```
Search Space for bloomDay = [1, 10, 3, 10, 2], m = 3, k = 1:
minDay = 1, maxDay = 10
Day D:        1    2  |   3    4   ...   10
Bouquets:     1    2  |   3    3   ...    5
canMake(3):   F    F  |   T    T   ...    T
                          ^
                   First 'true' = 3
```

---

## 3. Approach 1 — Brute Force (Linear Day Scan)

### Idea
Check if $m \times k > n$; if so, return `-1`. Otherwise, find $\min(\text{bloomDay})$ and $\max(\text{bloomDay})$. Iterate through every day $D$ from $\min$ to $\max$. For each day, run a linear pass to count contiguous bloomed flowers and form bouquets. The first day where $\text{bouquets} \ge m$ is our answer.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <climits>
using namespace std;

bool possibleOnDay(const vector<int>& bloomDay, int day, int m, int k) {
    int count = 0;
    int bouquets = 0;
    for (int b : bloomDay) {
        if (b <= day) {
            count++;
            if (count == k) {
                bouquets++;
                count = 0;
            }
        } else {
            count = 0; // broken contiguous sequence
        }
    }
    return bouquets >= m;
}

int minDaysLinear(const vector<int>& bloomDay, int m, int k) {
    int n = bloomDay.size();
    if ((long long)m * k > n) return -1;
    
    int minDay = *min_element(bloomDay.begin(), bloomDay.end());
    int maxDay = *max_element(bloomDay.begin(), bloomDay.end());
    
    for (int day = minDay; day <= maxDay; day++) {
        if (possibleOnDay(bloomDay, day, m, k)) {
            return day;
        }
    }
    return -1;
}
```

### Java Code
```java
class Solution {
    boolean possibleOnDay(int[] bloomDay, int day, int m, int k) {
        int count = 0;
        int bouquets = 0;
        for (int b : bloomDay) {
            if (b <= day) {
                count++;
                if (count == k) {
                    bouquets++;
                    count = 0;
                }
            } else {
                count = 0; // broken contiguous sequence
            }
        }
        return bouquets >= m;
    }
    
    int minDaysLinear(int[] bloomDay, int m, int k) {
        int n = bloomDay.length;
        if ((long)m * k > n) return -1;
        
        int minDay = min_element(bloomDay.begin(), bloomDay.end());
        int maxDay = max_element(bloomDay.begin(), bloomDay.end());
        
        for (int day = minDay; day <= maxDay; day++) {
            if (possibleOnDay(bloomDay, day, m, k)) {
                return day;
            }
        }
        return -1;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}((\max(\text{bloomDay}) - \min(\text{bloomDay})) \times n)$ — if $\max = 10^9$ and $n = 10^5$, this takes up to $10^{14}$ operations, resulting in Time Limit Exceeded.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary memory.
- **Why it's not good enough**: Testing every day sequentially wastes computation when the boolean outcome is monotonically sorted.

---

## 4. Approach 2 — Better (Sorted Unique Days Scan)

### Idea
Extract all unique bloom day values from `bloomDay`, sort them, and linearly test only these discrete bloom days. Since bouquet counts only change on days when at least one flower blooms, testing non-bloom days is redundant.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <set>
using namespace std;

int minDaysBetter(const vector<int>& bloomDay, int m, int k) {
    int n = bloomDay.size();
    if ((long long)m * k > n) return -1;
    
    set<int> uniqueDays(bloomDay.begin(), bloomDay.end());
    for (int day : uniqueDays) {
        if (possibleOnDay(bloomDay, day, m, k)) {
            return day;
        }
    }
    return -1;
}
```

### Java Code
```java
import java.util.*;

class Solution {
    int minDaysBetter(int[] bloomDay, int m, int k) {
        int n = bloomDay.length;
        if ((long)m * k > n) return -1;
        
        Set<Integer> uniqueDays(bloomDay.begin(), bloomDay.end());
        for (int day : uniqueDays) {
            if (possibleOnDay(bloomDay, day, m, k)) {
                return day;
            }
        }
        return -1;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n \log n + u \cdot n)$ where $u \le n$ is the number of unique days. If all bloom days are unique ($u = n$), time is $\mathcal{O}(n^2) = 10^{10}$ operations (still TLE).
- **Space Complexity**: $\mathcal{O}(n)$ to store unique days.
- **Why it's still not optimal**: It does not eliminate half the days per step; Binary Search on the range achieves $\mathcal{O}(n \log(\max))$ with $\mathcal{O}(1)$ space.

---

## 5. Approach 3 — Optimal (Binary Search on Answer Space)

### Idea
1. **Sanity Check**: If `(long long)m * k > n`, return `-1`.
2. **Search Range**: `low = *min_element(bloomDay)`, `high = *max_element(bloomDay)`.
3. **Binary Search**:
   - Compute `mid = low + (high - low) / 2`.
   - Run greedy helper `possibleOnDay(bloomDay, mid, m, k)`.
   - If `possible == true`: `mid` is a feasible day. Store `ans = mid` and search left `high = mid - 1` to find an earlier day.
   - Else: `mid` is not enough. Search right `low = mid + 1`.
4. Return `ans` (or `low` at termination).

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
private:
    bool canMakeBouquets(const vector<int>& bloomDay, int day, int m, int k) {
        int count = 0;
        int bouquets = 0;
        
        for (int b : bloomDay) {
            if (b <= day) {
                count++;
                if (count == k) {
                    bouquets++;
                    count = 0; // bouquet formed, reset contiguous flower counter
                }
            } else {
                count = 0;     // flower not bloomed, broken adjacency
            }
        }
        return bouquets >= m;
    }

public:
    int minDays(vector<int>& bloomDay, int m, int k) {
        int n = bloomDay.size();
        
        // 64-bit cast prevents overflow when m * k exceeds 2^31 - 1
        if ((long long)m * k > n) {
            return -1;
        }
        
        int low = *min_element(bloomDay.begin(), bloomDay.end());
        int high = *max_element(bloomDay.begin(), bloomDay.end());
        int ans = -1;
        
        while (low <= high) {
            int mid = low + (high - low) / 2;
            
            if (canMakeBouquets(bloomDay, mid, m, k)) {
                ans = mid;        // valid day, try to find earlier day on left
                high = mid - 1;
            } else {
                low = mid + 1;    // cannot make m bouquets, need more days
            }
        }
        
        return ans;
    }
};
```

### Java Code
```java
class Solution {

    boolean canMakeBouquets(int[] bloomDay, int day, int m, int k) {
        int count = 0;
        int bouquets = 0;
        
        for (int b : bloomDay) {
            if (b <= day) {
                count++;
                if (count == k) {
                    bouquets++;
                    count = 0; // bouquet formed, reset contiguous flower counter
                }
            } else {
                count = 0;     // flower not bloomed, broken adjacency
            }
        }
        return bouquets >= m;
    }

    int minDays(int[] bloomDay, int m, int k) {
        int n = bloomDay.length;
        
        // 64-bit cast prevents overflow when m * k exceeds 2^31 - 1
        if ((long)m * k > n) {
            return -1;
        }
        
        int low = min_element(bloomDay.begin(), bloomDay.end());
        int high = max_element(bloomDay.begin(), bloomDay.end());
        int ans = -1;
        
        while (low <= high) {
            int mid = low + (high - low) / 2;
            
            if (canMakeBouquets(bloomDay, mid, m, k)) {
                ans = mid;        // valid day, try to find earlier day on left
                high = mid - 1;
            } else {
                low = mid + 1;    // cannot make m bouquets, need more days
            }
        }
        
        return ans;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n \cdot \log_2(\max - \min))$ — binary search takes $\log_2(10^9) \approx 30$ iterations. In each iteration, we scan the array of size $n = 10^5$ once in linear time. Total operations: $30 \times 10^5 = 3 \times 10^6$, running in $\approx 20\text{ms}$.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Verifying bouquet feasibility on an arbitrary day requires inspecting all $n$ flowers ($\Omega(n)$ lower bound), and binary search achieves the optimal $\Omega(\log(\text{range}))$ query bound.

---

## 6. Dry Run

`bloomDay = [7, 7, 7, 7, 12, 7, 7]`, `m = 2`, `k = 3`. $n = 7$. Required flowers $= 2 \times 3 = 6 \le 7$.
Search space: `low = 7, high = 12`.

| Iteration | `low` | `high` | `mid` (day) | Bloomed Pattern (`b <= mid`) | Contiguous Groups | Bouquets Formed | `canMake` | Action |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|---|
| **1** | `7` | `12` | `9` | `[T, T, T, T, F, T, T]` | Group 1: 4 flowers $\implies 1$ bqt<br/>Group 2: 2 flowers $\implies 0$ bqt | **1** | $1 \ge 2$ (False) | `low = mid + 1 = 10` |
| **2** | `10` | `12` | `11` | `[T, T, T, T, F, T, T]` | Group 1: 4 flowers $\implies 1$ bqt<br/>Group 2: 2 flowers $\implies 0$ bqt | **1** | $1 \ge 2$ (False) | `low = mid + 1 = 12` |
| **3** | `12` | `12` | `12` | `[T, T, T, T, T, T, T]` | All 7 flowers bloomed $\implies \lfloor 7 / 3 \rfloor = 2$ bqts | **2** | $2 \ge 2$ (True) | `ans = 12, high = 11` |
| **Exit** | `12` | `11` | - | `low (12) > high (11)` | - | - | - | **Return `ans = 12`** ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- **Impossible from Start ($m \times k > n$)**: `bloomDay = [1, 10, 3, 10, 2], m = 3, k = 2` $\implies 3 \times 2 = 6 > 5 \implies$ returns `-1`.
- **$k = 1$ (Every flower forms a bouquet)**: Reduces to counting if total bloomed flowers $\ge m$.
- **All flowers bloom on the same day**: `bloomDay = [5, 5, 5, 5], m = 2, k = 2` $\implies$ returns `5`.

### Common Bugs to Avoid
- **32-Bit Signed Overflow on `m * k`**: When $m = 10^6$ and $k = 10^5$, $m \times k = 10^{11}$, which exceeds `INT_MAX` ($2.14 \times 10^9$) and overflows to a negative integer, bypassing the `m * k > n` check. Always cast: `(long long)m * k > n`.
- **Not Resetting Counter on Unbloomed Flower**: Forgetting `count = 0` inside the `else` branch, which erroneously connects non-adjacent flowers separated by an unbloomed flower.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is the greedy choice (forming a bouquet as soon as $k$ adjacent flowers are found) provably optimal?**  
  **A**: Suppose we have a contiguous segment of $L$ bloomed flowers. Delaying the start of a bouquet never increases the number of bouquets we can pack into $L$ (it can only leave fewer remaining flowers). By standard greedy exchange arguments, packing bouquets as early as possible within each contiguous bloomed component maximizes the total bouquet count $\sum \lfloor L_i / k 
floor$.

- **Q2: What if flowers did not need to be adjacent (any $k$ flowers could form a bouquet)?**  
  **A**: If adjacency is not required, the problem simplifies dramatically: we only need $m \times k$ total bloomed flowers anywhere. The answer is simply the $(m \times k)$-th smallest element in `bloomDay`, which can be found in $\mathcal{O}(n)$ time using **Quickselect (`std::nth_element`)** without binary search!

- **Q3: What if flowers wilt and die $W$ days after blooming (bloom window $[B_i, B_i + W]$)?**  
  **A**: The predicate is no longer globally monotonic with respect to $D$ because older flowers wilt as new ones bloom. Binary search on $D$ is no longer applicable; we would need an event-driven sweep-line algorithm with a Disjoint Set Union (DSU) or Segment Tree in $\mathcal{O}(n \log n)$.

- **Q4: How does this problem compare to LeetCode 1011 (Capacity to Ship Packages)?**  
  **A**: Both use binary search over a continuous answer range. In Bouquets, the partition is constrained by a fixed chunk size $k$ and variable day $D$. In Ship Packages, the partition is constrained by a fixed day limit $D$ and variable chunk capacity $C$.

- **Q5: Can we optimize the search range lower bound?**  
  **A**: Yes, the minimum possible day cannot be smaller than the $k$-th smallest element in `bloomDay` (since at least one bouquet of size $k$ is required).

---

## 9. Tags & Related Problems

- **Tags**: `Binary Search`, `BS on Answers`, `Greedy`, `LeetCode-1482`, `Medium`
- **Related problems to practice next**:
  - **Koko Eating Bananas (LeetCode 875)**: Symmetrical BS on answers.
  - **Find the Smallest Divisor Given a Threshold (LeetCode 1283)**: Arithmetic monotonic predicate.
  - **Capacity to Ship Packages within D Days (LeetCode 1011)**: Contiguous subarray packing.
