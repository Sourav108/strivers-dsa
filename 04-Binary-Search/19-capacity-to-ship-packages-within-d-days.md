# Capacity to Ship Packages within D Days (Step 4.2 — BS on Answers)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [LeetCode #1011 - Capacity To Ship Packages Within D Days](https://leetcode.com/problems/capacity-to-ship-packages-within-d-days/) | [TakeUForward](https://takeuforward.org/binary-search/capacity-to-ship-packages-within-d-days/)
- **Difficulty**: Medium
- **Statement**: A conveyor belt has packages that must be shipped from one port to another within `days` days. The $i$-th package on the conveyor belt has a weight of `weights[i]`. Each day, we load the ship with packages on the conveyor belt (in the order given by `weights`). We may not load more weight than the maximum weight capacity of the ship. Return the least weight capacity of the ship that will result in all the packages on the conveyor belt being shipped within `days` days.

---

## 1. Problem, Restated

Find the minimum ship capacity $C$ such that we can partition the array `weights` into at most `days` contiguous subarrays, where the sum of weights in each subarray does not exceed $C$.

- **Order Constraint**: Packages must be loaded in the exact sequence given by the conveyor belt.
- **Input**: Vector of package weights `weights` of size $n$, and integer `days`.
- **Output**: Minimum integer ship capacity $C$.
- **Constraints**: $1 \le \text{days} \le n \le 5 \times 10^4$, $1 \le \text{weights}[i] \le 500$.

---

## 2. Intuition & Pattern

### The Contiguous Partitioning Predicate
Consider the bounds of the search space for capacity $C$:
1. **Minimum Possible Capacity (`low`)**: $\max(\text{weights})$. The ship must be capable of carrying the heaviest single package; otherwise that package can never be shipped on any day.
2. **Maximum Possible Capacity (`high`)**: $\sum \text{weights}$. With a capacity equal to the total weight of all packages, the ship can transport everything in exactly $1$ day.

As capacity $C$ **increases**, the number of days required to transport all packages **monotonically decreases**.

The predicate $\text{canShip}(C) = (\text{days needed with capacity } C \le \text{days})$ forms a monotonic boolean array over $[\max(\text{weights}), \sum \text{weights}]$:
- `[false, false, ..., false, true, true, ..., true]`

We want the **FIRST `true`** (the minimum valid capacity $C$).

```
Search Space for weights = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10], days = 5:
low = max(weights) = 10, high = sum(weights) = 55
Capacity C:     10   11   12   13   14  |  15   16   ...   55
Days needed:     8    8    7    6    6  |   5    5   ...    1
<= 5 days:       F    F    F    F    F  |   T    T   ...    T
                                            ^
                                     First 'true' = 15
```

---

## 3. Approach 1 — Brute Force (Linear Capacity Scan)

### Idea
Iterate capacity $C$ starting from $\max(\text{weights})$ up to $\sum \text{weights}$. For each capacity, simulate loading the ship sequentially and count the days required. Return the first capacity that ships all packages in $\le \text{days}$.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <numeric>
using namespace std;

int findDaysNeeded(const vector<int>& weights, int capacity) {
    int days = 1;
    int currentLoad = 0;
    
    for (int w : weights) {
        if (currentLoad + w > capacity) {
            days++;
            currentLoad = w; // start new day with current package
        } else {
            currentLoad += w;
        }
    }
    return days;
}

int shipWithinDaysLinear(const vector<int>& weights, int days) {
    int maxVal = *max_element(weights.begin(), weights.end());
    int totalSum = accumulate(weights.begin(), weights.end(), 0);
    
    for (int cap = maxVal; cap <= totalSum; cap++) {
        if (findDaysNeeded(weights, cap) <= days) {
            return cap;
        }
    }
    return totalSum;
}
```

### Java Code
```java
class Solution {
    int findDaysNeeded(int[] weights, int capacity) {
        int days = 1;
        int currentLoad = 0;
        
        for (int w : weights) {
            if (currentLoad + w > capacity) {
                days++;
                currentLoad = w; // start new day with current package
            } else {
                currentLoad += w;
            }
        }
        return days;
    }
    
    int shipWithinDaysLinear(int[] weights, int days) {
        int maxVal = max_element(weights.begin(), weights.end());
        int totalSum = accumulate(weights.begin(), weights.end(), 0);
        
        for (int cap = maxVal; cap <= totalSum; cap++) {
            if (findDaysNeeded(weights, cap) <= days) {
                return cap;
            }
        }
        return totalSum;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}((\sum \text{weights} - \max(\text{weights})) \times n)$ — in the worst case with $n = 5 \times 10^4$ and weights $= 500$, $\sum = 2.5 \times 10^7$. Linear scan requires $2.5 \times 10^7 \times 5 \times 10^4 = 1.25 \times 10^{12}$ operations, triggering Time Limit Exceeded.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary memory.
- **Why it's not good enough**: Linear scan fails to exploit the monotonic order of required days.

---

## 4. Approach 2 — Better (Bounded Lower-Bound Linear Scan)

### Idea
The theoretical minimum capacity cannot be smaller than $\lceil \sum \text{weights} / \text{days} \rceil$. We can start our linear search from $\max(\max(\text{weights}), \lceil \sum \text{weights} / \text{days} \rceil)$.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <numeric>
using namespace std;

int shipWithinDaysBetter(const vector<int>& weights, int days) {
    int maxVal = *max_element(weights.begin(), weights.end());
    int totalSum = accumulate(weights.begin(), weights.end(), 0);
    
    int startCap = max(maxVal, (totalSum + days - 1) / days);
    for (int cap = startCap; cap <= totalSum; cap++) {
        if (findDaysNeeded(weights, cap) <= days) {
            return cap;
        }
    }
    return totalSum;
}
```

### Java Code
```java
class Solution {
    int shipWithinDaysBetter(int[] weights, int days) {
        int maxVal = max_element(weights.begin(), weights.end());
        int totalSum = accumulate(weights.begin(), weights.end(), 0);
        
        int startCap = Math.max(maxVal, (totalSum + days - 1) / days);
        for (int cap = startCap; cap <= totalSum; cap++) {
            if (findDaysNeeded(weights, cap) <= days) {
                return cap;
            }
        }
        return totalSum;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}((\text{totalSum} - \text{startCap}) \times n)$ — still linear in the value domain.
- **Space Complexity**: $\mathcal{O}(1)$ space.
- **Why it's still not optimal**: It only tightens the search start; binary search is required to achieve logarithmic iterations.

---

## 5. Approach 3 — Optimal (Binary Search on Answer Space)

### Idea
1. **Search Space Bounds**:
   - `low = *max_element(weights.begin(), weights.end())` (cannot be smaller than largest single package).
   - `high = accumulate(weights.begin(), weights.end(), 0)` (sum of all packages).
2. **Binary Search**:
   - Compute `mid = low + (high - low) / 2`.
   - Calculate `daysNeeded = countDays(weights, mid)`.
   - If `daysNeeded <= days`: `mid` is a valid capacity. Record `ans = mid` and search left `high = mid - 1` to find a smaller valid capacity.
   - Else: `mid` is too small (`daysNeeded > days`). Search right `low = mid + 1`.
3. Return `ans` (or `low` at loop termination).

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <numeric>
using namespace std;

class Solution {
private:
    int countDays(const vector<int>& weights, int capacity) {
        int days = 1;
        int currentLoad = 0;
        
        for (int w : weights) {
            if (currentLoad + w > capacity) {
                days++;          // capacity exceeded, dispatch ship and start next day
                currentLoad = w; // place current package on new day's ship
            } else {
                currentLoad += w;
            }
        }
        return days;
    }

public:
    int shipWithinDays(vector<int>& weights, int days) {
        int low = *max_element(weights.begin(), weights.end());
        int high = accumulate(weights.begin(), weights.end(), 0);
        int ans = high;
        
        while (low <= high) {
            int mid = low + (high - low) / 2;
            
            if (countDays(weights, mid) <= days) {
                ans = mid;        // valid capacity, try to find smaller on left
                high = mid - 1;
            } else {
                low = mid + 1;    // capacity too small, need larger capacity
            }
        }
        
        return ans;
    }
};
```

### Java Code
```java
class Solution {

    int countDays(int[] weights, int capacity) {
        int days = 1;
        int currentLoad = 0;
        
        for (int w : weights) {
            if (currentLoad + w > capacity) {
                days++;          // capacity exceeded, dispatch ship and start next day
                currentLoad = w; // place current package on new day's ship
            } else {
                currentLoad += w;
            }
        }
        return days;
    }

    int shipWithinDays(int[] weights, int days) {
        int low = max_element(weights.begin(), weights.end());
        int high = accumulate(weights.begin(), weights.end(), 0);
        int ans = high;
        
        while (low <= high) {
            int mid = low + (high - low) / 2;
            
            if (countDays(weights, mid) <= days) {
                ans = mid;        // valid capacity, try to find smaller on left
                high = mid - 1;
            } else {
                low = mid + 1;    // capacity too small, need larger capacity
            }
        }
        
        return ans;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n \cdot \log_2(\sum \text{weights} - \max(\text{weights})))$ — binary search takes $\log_2(2.5 \times 10^7) \approx 25$ iterations. In each iteration, we scan $n = 5 \times 10^4$ packages in linear time. Total operations: $25 \times 5 \times 10^4 = 1.25 \times 10^6$, running in $\approx 12\text{ms}$.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Linear scan is required to verify any capacity on a sequential conveyor belt, and binary search achieves the theoretical lower bound $\Omega(\log(\text{range}))$.

---

## 6. Dry Run

`weights = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]`, `days = 5`.
`low = max = 10, high = sum = 55`.

| Iteration | `low` | `high` | `mid` (cap) | Daily Grouping Breakdown (Sum $\le$ cap) | Days Used | `days <= 5` | Action |
|:---:|:---:|:---:|:---:|---|:---:|:---:|---|
| **1** | `10` | `55` | `32` | D1: [1..7] (28), D2: [8,9,10] (27) | **2** | $2 \le 5$ (True) | `ans = 32, high = 31` |
| **2** | `10` | `31` | `20` | D1: [1..5] (15), D2: [6,7] (13), D3: [8,9] (17), D4: [10] (10) | **4** | $4 \le 5$ (True) | `ans = 20, high = 19` |
| **3** | `10` | `19` | `14` | D1: [1..4] (10), D2: [5,6] (11), D3: [7] (7), D4: [8] (8), D5: [9] (9), D6: [10] (10) | **6** | $6 \le 5$ (False) | `low = mid + 1 = 15` |
| **4** | `15` | `19` | `17` | D1: [1..5] (15), D2: [6,7] (13), D3: [8,9] (17), D4: [10] (10) | **4** | $4 \le 5$ (True) | `ans = 17, high = 16` |
| **5** | `15` | `16` | `15` | D1: [1..5] (15), D2: [6,7] (13), D3: [8] (8), D4: [9] (9), D5: [10] (10) | **5** | $5 \le 5$ (True) | `ans = 15, high = 14` |
| **Exit** | `15` | `14` | - | `low (15) > high (14)` | - | - | **Return `ans = 15`** ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- **`days == 1`**: The ship must carry all packages in 1 trip $\implies$ returns $\sum \text{weights}$.
- **`days == n`**: Each package can be carried on a separate day $\implies$ returns $\max(\text{weights})$.
- **All packages have equal weight**: `weights = [3, 3, 3, 3], days = 2` $\implies$ returns $6$.

### Common Bugs to Avoid
- **Setting `low = 1` instead of `*max_element`**: If capacity is set smaller than the heaviest package (e.g. $cap = 5$ for a package of weight $10$), that package alone exceeds capacity on day 1, producing an infinite loop or corrupting the simulation.
- **Starting `days = 0` in counter**: `days` must start at `1` because shipping at least one package requires the first day.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Which other classic interview problems are mathematically identical to this problem?**  
  **A**:
  1. **Book Allocation Problem**: Allocate books to $M$ students minimizing maximum pages.
  2. **Painter's Partition Problem**: Paint $N$ boards with $K$ painters minimizing maximum time.
  3. **Split Array Largest Sum (LeetCode 410)**: Split array into $K$ non-empty subarrays minimizing largest sum.
  All four problems share the **exact same code and binary search pattern**.

- **Q2: Why is the greedy packing strategy provably optimal?**  
  **A**: Because packages cannot be reordered. To minimize total days, each day should pack as many contiguous packages as possible without exceeding capacity $C$. Putting fewer packages on day $D$ can never decrease the total days required.

- **Q3: What if packages could be reordered freely (not on a conveyor belt)?**  
  **A**: If reordering is allowed, the problem transforms into the **Bin Packing Problem** with a fixed number of bins, which is **NP-Hard**. It would require backtracking $\mathcal{O}(k^n)$ or approximation heuristics. The sequential constraint is what enables the $\mathcal{O}(n \log(\sum))$ polynomial solution.

- **Q4: How can we prevent integer overflow in `totalSum`?**  
  **A**: If package weights can be up to $10^9$ and $n = 10^5$, $\sum \text{weights} = 10^{14}$. Using `long long totalSum = accumulate(..., 0LL)` and `long long mid` handles arbitrary 64-bit inputs safely.

- **Q5: Can we terminate the `countDays` function early?**  
  **A**: Yes, if `days > targetDays`, we can immediately return `days` and break the loop without finishing the rest of the array.

---

## 9. Tags & Related Problems

- **Tags**: `Binary Search`, `BS on Answers`, `Greedy`, `LeetCode-1011`, `Medium`
- **Related problems to practice next**:
  - **Book Allocation Problem**: Symmetrical student-book allocation.
  - **Painter's Partition Problem**: Symmetrical painter-board allocation.
  - **Split Array Largest Sum (LeetCode 410)**: Pure mathematical partition formulation.
  - **Aggressive Cows**: BS on Answers with distance maximization.
