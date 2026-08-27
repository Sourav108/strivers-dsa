# Aggressive Cows (Minimise Maximum Distance) (Step 4.2 — BS on Answers)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Aggressive Cows (Minimise Maximum Distance)](https://takeuforward.org/data-structure/aggressive-cows-detailed-solution/)
- **Difficulty**: Hard
- **Statement**: You are given an array `stalls` of size $n$ representing coordinates of stalls on a straight line, and an integer $k$ representing the number of aggressive cows. Assign stalls to $k$ cows such that the minimum distance between any two cows is as large as possible (Maximise the Minimum Distance). Return the largest minimum distance.

---

## 1. Problem, Restated

Place $k$ cows into a subset of $n$ stall positions such that no two cows are closer than distance $D$, while maximizing $D$.

- **Input**: Vector of integers `arr`/`stalls`/`nums`, plus query parameters.
- **Output**: Minimized or maximized value meeting the specification.
- **Constraints**: $1 \le n \le 10^5$, values fit in 32/64-bit integer ranges.

---

## 2. Intuition & Pattern

Sort stall coordinates so we can place cows sequentially. The minimum possible distance is $1$, and the maximum possible distance is `stalls[n-1] - stalls[0]`. For a guessed distance $D$, we check if we can place all $k$ cows greedily (1st cow at `stalls[0]`, next cow at the first stall with $\text{pos} \ge \text{lastPos} + D$). As $D$ increases, the number of cows we can fit monotonically decreases: `[true, true, ..., true, false, false]`. We want the **LAST `true`**.

- **Underlying Pattern**: `Maximise the Minimum Distance (BS on Distance Space)`.
- **The "Aha!" Moment**: Recognizing how the feasibility predicate divides the answer domain into monotonic boolean halves.

---

## 3. Approach 1 — Brute Force (Linear Range Scan)

### Idea
Linear Scan of Distance: Sort stalls. Iterate distance $D$ from $1$ to `stalls[n-1] - stalls[0]`. Test each $D$ greedily. Return the last $D$ where all $k$ cows fit.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

bool canPlaceCows(const vector<int>& stalls, int dist, int k) {
    int countCows = 1;
    int lastPos = stalls[0];
    for (size_t i = 1; i < stalls.size(); i++) {
        if (stalls[i] - lastPos >= dist) {
            countCows++;
            lastPos = stalls[i];
        }
        if (countCows >= k) return true;
    }
    return false;
}

int aggressiveCowsLinear(vector<int>& stalls, int k) {
    sort(stalls.begin(), stalls.end());
    int maxDist = stalls.back() - stalls.front();
    int ans = 1;
    for (int d = 1; d <= maxDist; d++) {
        if (canPlaceCows(stalls, d, k)) ans = d;
        else break;
    }
    return ans;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n \log n + (\text{maxDist}) \times n)$ — linear distance scan takes up to $10^9 \times 10^5 = 10^{14}$ operations $\implies$ TLE.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why it's not good enough**: Iterating distance by $1$ is too slow when distances span up to $10^9$.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal (Binary Search on Answer Space)

### Idea
Binary Search on Distance Range: Sort stalls. `low = 1, high = stalls[n-1] - stalls[0]`. While `low <= high`, if `canPlaceCows(stalls, mid, k)` is true, record `ans = mid` and search right `low = mid + 1` (maximize distance); else search left `high = mid - 1`.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
private:
    bool canPlaceCows(const vector<int>& stalls, int dist, int k) {
        int countCows = 1;
        int lastPos = stalls[0];
        
        for (size_t i = 1; i < stalls.size(); i++) {
            if (stalls[i] - lastPos >= dist) {
                countCows++;
                lastPos = stalls[i];
            }
            if (countCows >= k) return true;
        }
        return false;
    }

public:
    int aggressiveCows(vector<int>& stalls, int k) {
        sort(stalls.begin(), stalls.end());
        int n = stalls.size();
        
        int low = 1;
        int high = stalls[n - 1] - stalls[0];
        int ans = 1;
        
        while (low <= high) {
            int mid = low + (high - low) / 2;
            
            if (canPlaceCows(stalls, mid, k)) {
                ans = mid;       // distance feasible, try to maximize further
                low = mid + 1;
            } else {
                high = mid - 1;  // distance too large, reduce distance
            }
        }
        
        return ans; // or high at loop termination
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n \log n + n \log_2(\text{maxDist}))$ — sorting takes $\mathcal{O}(n \log n)$ and binary search takes $\approx 30$ iterations of $\mathcal{O}(n)$ checks.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Optimal bound for minimax distance optimization on 1D point sets.

---

## 6. Dry Run

`stalls = [0, 3, 4, 7, 10, 9]`, $k = 4$. Sorted: `[0, 3, 4, 7, 9, 10]`

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | low = 1, high = 10 - 0 = 10 | ready |
| `Iter 1` | mid = 5. Place at 0, 7 (only 2 cows fit < 4) | canPlace=F -> high = 4 |
| `Iter 2` | mid = 2. Place at 0, 3, 7, 9 (4 cows fit >= 4) | canPlace=T -> ans = 2, low = 3 |
| `Iter 3` | mid = 3. Place at 0, 3, 7, 10 (4 cows fit >= 4) | canPlace=T -> ans = 3, low = 4 |
| `Iter 4` | mid = 4. Place at 0, 4, 9 (only 3 cows fit < 4) | canPlace=F -> high = 3 |
| `Exit` | low (4) > high (3) -> loop ends | Return ans = 3 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- k = 2 (cows placed at extreme ends `stalls[0]` and `stalls[n-1]` $\implies$ distance is `stalls[n-1] - stalls[0]`).
- k = n (cows placed in every stall $\implies$ answer is minimum adjacent difference).
- Stalls not initially sorted (must sort first).

### Common Bugs to Avoid
- Forgetting to sort stalls array before running binary search.
- Setting `ans = mid` and moving `high = mid - 1` instead of `low = mid + 1` (we want to MAXIMIZE the minimum distance, so valid guesses search right).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is placing the first cow at stalls[0] always optimal?**  
  **A**: By greedy exchange argument: placing the first cow at any stall $i > 0$ leaves less remaining space to the right for the other $k - 1$ cows. Starting at `stalls[0]` maximizes available rightward room.

- **Q2: What is the dual problem of Aggressive Cows?**  
  **A**: Book Allocation / Ship Packages minimizes the maximum partition. Aggressive Cows maximizes the minimum separation distance.

- **Q3: What if stalls are in a 2D plane instead of 1D line?**  
  **A**: In 2D, the problem transforms into the Geometric Independent Set / Disc Packing problem, which is **NP-Hard**.

- **Q4: Can stalls have negative coordinates?**  
  **A**: Yes, sorting handles negative numbers naturally, and distances `stalls[i] - stalls[j]` remain non-negative.

- **Q5: How to handle millions of stalls distributed across machines?**  
  **A**: Sort stalls globally using distributed merge sort, then run parallel binary search partition validation.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Search`, `BS on Answers`, `Greedy`, `Hard`
- **Related problems to practice next**:
- **Book Allocation Problem**: Dual minimax partition problem.
- **Painter's Partition Problem**: Subarray time minimization.
- **Koko Eating Bananas**: BS on feasibility.
