# Koko Eating Bananas (Step 4.2 — BS on Answers)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [LeetCode #875 - Koko Eating Bananas](https://leetcode.com/problems/koko-eating-bananas/) | [TakeUForward](https://takeuforward.org/binary-search/koko-eating-bananas/)
- **Difficulty**: Medium
- **Statement**: Koko loves to eat bananas. There are $n$ piles of bananas, the $i$-th pile has `piles[i]` bananas. The guards have gone and will come back in $h$ hours. Koko can decide her bananas-per-hour eating speed of $k$. Each hour, she chooses some pile of bananas and eats $k$ bananas from that pile. If the pile has less than $k$ bananas, she eats all of them instead and will not eat any more bananas during this hour. Return the minimum integer $k$ such that she can eat all the bananas within $h$ hours.

---

## 1. Problem, Restated

Find the minimum eating speed $k$ (bananas per hour) such that Koko can finish all piles within $h$ hours. For each pile of size $p$, the time spent eating that pile is $\lceil p / k \rceil$ hours. We need the smallest integer $k \ge 1$ where:
$$\sum_{i=0}^{n-1} \left\lceil \frac{\text{piles}[i]}{k} \right\rceil \le h$$

- **Input**: Vector of integers `piles` of length $n$, and an integer $h$.
- **Output**: Minimum integer speed $k$.
- **Constraints**: $1 \le n \le 10^4$, $n \le h \le 10^9$, $1 \le \text{piles}[i] \le 10^9$.

---

## 2. Intuition & Pattern

### Monotonic Feasibility Predicate
Let $f(k)$ be the total hours required to eat all bananas at speed $k$:
- If $k = 1$, total hours is maximum ($\sum 	ext{piles}[i]$).
- If $k = \max(\text{piles})$, Koko finishes each pile in exactly $1$ hour, taking $n$ hours total. Since $h \ge n$, speed $k = \max(\text{piles})$ is always feasible.
- As speed $k$ **increases**, the required hours $f(k)$ **monotonically decreases**.

The feasibility condition $f(k) \le h$ divides the answer space $[1, \max(\text{piles})]$ into:
- `[false, false, ..., false, true, true, ..., true]`

We want the **FIRST `true`** (the minimum valid speed $k$).

```
Search Space for piles = [3, 6, 7, 11], h = 8 (max = 11):
k:       1    2    3  |  4    5    6  ...  11
Hours:  27   15   10  |  8    8    6  ...   4
<= 8:    F    F    F  |  T    T    T  ...   T
                         ^
                  First 'true' = 4
```

---

## 3. Approach 1 — Brute Force (Linear Scan)

### Idea
Iterate speed $k$ linearly from $1$ up to $\max(\text{piles})$. For each $k$, calculate total hours. The first speed $k$ that yields $\text{totalHours} \le h$ is returned.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <cmath>
using namespace std;

int minEatingSpeedLinear(const vector<int>& piles, int h) {
    int maxVal = *max_element(piles.begin(), piles.end());
    
    for (int k = 1; k <= maxVal; k++) {
        long long totalHours = 0;
        for (int pile : piles) {
            totalHours += (pile + k - 1) / k; // integer ceiling division
        }
        if (totalHours <= h) {
            return k;
        }
    }
    return maxVal;
}
```

### Java Code
```java
class Solution {
    int minEatingSpeedLinear(int[] piles, int h) {
        int maxVal = max_element(piles.begin(), piles.end());
        
        for (int k = 1; k <= maxVal; k++) {
            long totalHours = 0;
            for (int pile : piles) {
                totalHours += (pile + k - 1) / k; // integer ceiling division
            }
            if (totalHours <= h) {
                return k;
            }
        }
        return maxVal;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\max(\text{piles}) \times n)$ — in the worst case where $\max(\text{piles}) = 10^9$ and $n = 10^4$, this takes $10^{13}$ operations, triggering Time Limit Exceeded.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why it's not good enough**: Linear search increments by $1$, wasting millions of iterations on infeasible speeds.

---

## 4. Approach 2 — Better (Bounded Lower-Bound Linear Scan)

### Idea
Instead of starting $k$ at $1$, compute a theoretical absolute minimum lower bound: $\text{totalBananas} / h$. Even if bananas were continuous without pile boundaries, Koko must eat at least $\lceil \sum \text{piles} / h \rceil$ bananas per hour. We can start our scan from $\max(1, \lfloor \sum \text{piles} / h \rfloor)$.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <numeric>
using namespace std;

int minEatingSpeedBetter(const vector<int>& piles, int h) {
    long long totalBananas = 0;
    int maxVal = 0;
    for (int p : piles) {
        totalBananas += p;
        maxVal = max(maxVal, p);
    }
    
    int startK = max(1LL, totalBananas / h);
    for (int k = startK; k <= maxVal; k++) {
        long long totalHours = 0;
        for (int pile : piles) {
            totalHours += (pile + k - 1) / k;
        }
        if (totalHours <= h) {
            return k;
        }
    }
    return maxVal;
}
```

### Java Code
```java
class Solution {
    int minEatingSpeedBetter(int[] piles, int h) {
        long totalBananas = 0;
        int maxVal = 0;
        for (int p : piles) {
            totalBananas += p;
            maxVal = Math.max(maxVal, p);
        }
        
        int startK = Math.max(1LL, totalBananas / h);
        for (int k = startK; k <= maxVal; k++) {
            long totalHours = 0;
            for (int pile : piles) {
                totalHours += (pile + k - 1) / k;
            }
            if (totalHours <= h) {
                return k;
            }
        }
        return maxVal;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}((\max(\text{piles}) - \text{avg}) \times n)$ — still linear in the value range, failing on skewed pile distributions (e.g. `[1, 1, 10^9]`).
- **Space Complexity**: $\mathcal{O}(1)$ space.
- **Why it's still not optimal**: It only tightens the starting point; binary search is required to achieve logarithmic time.

---

## 5. Approach 3 — Optimal (Binary Search on Answer Space)

### Idea
1. Set the search space: `low = 1`, `high = *max_element(piles.begin(), piles.end())`.
2. While `low <= high`:
   - Compute `mid = low + (high - low) / 2`.
   - Calculate `hours = calculateTotalHours(piles, mid)` using 64-bit integer accumulator.
   - If `hours <= h`: `mid` is a valid speed. Record `ans = mid` and try to find a smaller valid speed on the left: `high = mid - 1`.
   - Else: `mid` is too slow (`hours > h`). Search on the right: `low = mid + 1`.
3. Return `ans` (or `low` at loop termination).

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

long long calculateTotalHours(const vector<int>& piles, int speed) {
    long long totalHours = 0;
    for (int pile : piles) {
        // Safe integer ceiling division: ceil(pile / speed) = (pile + speed - 1) / speed
        totalHours += ((long long)pile + speed - 1) / speed;
    }
    return totalHours;
}

int minEatingSpeed(const vector<int>& piles, int h) {
    int low = 1;
    int high = *max_element(piles.begin(), piles.end());
    int ans = high;
    
    while (low <= high) {
        int mid = low + (high - low) / 2;
        long long totalHours = calculateTotalHours(piles, mid);
        
        if (totalHours <= h) {
            ans = mid;        // valid speed, try to minimize further
            high = mid - 1;
        } else {
            low = mid + 1;    // too slow, increase speed
        }
    }
    
    return ans;
}
```

### Java Code
```java
class Solution {
    long calculateTotalHours(int[] piles, int speed) {
        long totalHours = 0;
        for (int pile : piles) {
            // Safe integer ceiling division: ceil(pile / speed) = (pile + speed - 1) / speed
            totalHours += ((long)pile + speed - 1) / speed;
        }
        return totalHours;
    }
    
    int minEatingSpeed(int[] piles, int h) {
        int low = 1;
        int high = max_element(piles.begin(), piles.end());
        int ans = high;
        
        while (low <= high) {
            int mid = low + (high - low) / 2;
            long totalHours = calculateTotalHours(piles, mid);
            
            if (totalHours <= h) {
                ans = mid;        // valid speed, try to minimize further
                high = mid - 1;
            } else {
                low = mid + 1;    // too slow, increase speed
            }
        }
        
        return ans;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n \cdot \log_2(\max(\text{piles})))$ — binary search takes $\log_2(10^9) \approx 30$ iterations. In each iteration, we scan the array of size $n$ once. Total operations: $30 \times 10^4 = 3 \times 10^5$, running in $\approx 8\text{ms}$.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: $n$ operations are required to evaluate feasibility, and binary search achieves the information-theoretic lower bound $\Omega(\log(\text{range}))$ over the answer interval.

---

## 6. Dry Run

`piles = [3, 6, 7, 11]`, `h = 8`. Search range: `low = 1, high = 11`.

| Iteration | `low` | `high` | `mid` (speed) | Hours Breakdown $\sum \lceil p / \text{mid} \rceil$ | Total Hours | `total <= 8` | Action |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|---|
| **1** | `1` | `11` | `6` | $\lceil 3/6 \rceil + \lceil 6/6 \rceil + \lceil 7/6 \rceil + \lceil 11/6 \rceil = 1 + 1 + 2 + 2$ | **6** | $6 \le 8$ (True) | `ans = 6, high = 5` |
| **2** | `1` | `5` | `3` | $\lceil 3/3 \rceil + \lceil 6/3 \rceil + \lceil 7/3 \rceil + \lceil 11/3 \rceil = 1 + 2 + 3 + 4$ | **10** | $10 \le 8$ (False) | `low = mid + 1 = 4` |
| **3** | `4` | `5` | `4` | $\lceil 3/4 \rceil + \lceil 6/4 \rceil + \lceil 7/4 \rceil + \lceil 11/4 \rceil = 1 + 2 + 2 + 3$ | **8** | $8 \le 8$ (True) | `ans = 4, high = 3` |
| **Exit** | `4` | `3` | - | `low (4) > high (3)` | - | - | **Return `ans = 4`** ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- **$h == n$ (hours equals number of piles)**: Koko must eat at least one full pile per hour. The answer is strictly $\max(\text{piles})$.
- **Single Pile ($n = 1$)**: `piles = [30], h = 5` $\implies \lceil 30 / 5 \rceil = 6$.
- **Huge Values ($h = 10^9, \text{piles}[i] = 10^9$)**: Handled without overflow by `long long` for `totalHours`.

### Common Bugs to Avoid
- **32-Bit Integer Overflow in Total Hours**: When speed $k = 1$ and $n = 10^5, \text{piles}[i] = 10^9$, total hours is $10^{14}$, which overflows 32-bit signed integer (`INT_MAX` $\approx 2.14 \times 10^9$). Always use `long long totalHours`.
- **Floating-Point Precision in Ceiling**: Writing `ceil((double)pile / speed)` can introduce floating-point precision truncation bugs for large numbers. Always use pure integer ceiling: `((long long)pile + speed - 1) / speed`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is `((long long)pile + speed - 1) / speed` mathematically equivalent to `ceil(pile / speed)`?**  
  **A**: For any positive integers $a, b$: by division algorithm $a = q \cdot b + r$ where $0 \le r < b$. If $r = 0$, $(a + b - 1) / b = (qb + b - 1)/b = q$. If $r > 0$, $(a + b - 1)/b = (qb + r + b - 1)/b = q + 1$. This exact identity produces $\lceil a/b \rceil$ using purely integer arithmetic without floating-point overhead.

- **Q2: Can the search space lower bound be initialized to $\lceil \sum \text{piles} / h \rceil$ instead of $1$?**  
  **A**: Yes! Summing all bananas takes $\mathcal{O}(n)$ and gives an absolute mathematical lower bound: $k \ge \lceil \sum \text{piles} / h \rceil$. This slightly reduces the search space iterations, though asymptotic complexity remains $\mathcal{O}(n \log(\max))$.

- **Q3: How does this problem relate to LeetCode 1011 (Capacity to Ship Packages within D Days)?**  
  **A**: Both problems belong to the **BS on Answers** paradigm. In Koko, elements can be eaten in pieces across multiple hours. In Ship Packages, items cannot be split and must remain contiguous in order, changing only the validation helper function $\text{canShip}(capacity)$.

- **Q4: What if Koko is allowed to eat from multiple piles within the same hour if bananas remain?**  
  **A**: If pile transitions have zero penalty, the problem reduces to a continuous stream: $k = \lceil \sum \text{piles} / h \rceil$ in $\mathcal{O}(n)$ time without binary search! The pile restriction is what introduces the step-function nonlinearity that necessitates binary search.

- **Q5: How would you parallelize this for $10^8$ piles across a cluster (MapReduce)?**  
  **A**: The coordinator binary searches $k \in [1, 10^9]$. For each guessed $k$, worker nodes compute local $\sum \lceil p_i / k \rceil$ in parallel, reducing to a global sum in $\mathcal{O}(\log(\text{nodes}))$ time per guess.

---

## 9. Tags & Related Problems

- **Tags**: `Binary Search`, `BS on Answers`, `LeetCode-875`, `Medium`
- **Related problems to practice next**:
  - **Minimum Days to Make M Bouquets (LeetCode 1482)**: BS on answer with contiguous subarray constraints.
  - **Capacity to Ship Packages within D Days (LeetCode 1011)**: BS on conveyor capacity.
  - **Find the Smallest Divisor Given a Threshold (LeetCode 1283)**: Symmetrical divisor search.
