# Minimum Number of Platforms Required for a Railway (Step 12.2 — Medium / Hard)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Minimum Number of Platforms Required for a Railway](https://takeuforward.org/data-structure/minimum-number-of-platforms-required-for-a-railway/)
- **Difficulty**: Medium
- **Statement**: Given arrival and departure times of all trains that reach a railway station, find the minimum number of platforms required for the railway station so that no train is kept waiting.

---

## 1. Problem, Restated

Find the maximum number of overlapping train intervals at any point in time using Two Pointers on sorted arrival/departure arrays.

- **Input**: Parameters specified.
- **Output**: Optimal value / boolean / transformed list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Platform count increases by +1 whenever a train arrives, and decreases by -1 whenever a train departs. We do NOT need to track which train is which! Sort `arr` and `dep` independently in ascending order. Two pointers `i = 0` (arrival) and `j = 0` (departure): 1) If `arr[i] <= dep[j]`: train arrives before another leaves $\implies$ `platforms++`, `maxPlatforms = max(maxPlatforms, platforms)`, `i++`. 2) Else: train departs $\implies$ `platforms--`, `j++`.

- **Underlying Pattern**: `Independent Event Timeline Sweep (Arrivals vs Departures)`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
For each train, check all other trains to count overlapping intervals in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
// O(N^2) overlap count
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Quadratic interval comparisons.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard greedy strategy below directly achieves optimal $\mathcal{O}(N \log N)$ or $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Sorted Event Timeline Two-Pointer Sweep in $\mathcal{O}(N \log N)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int findPlatform(int arr[], int dep[], int n) {
        sort(arr, arr + n);
        sort(dep, dep + n);
        
        int i = 0, j = 0;
        int currentPlatforms = 0, maxPlatforms = 0;
        
        while (i < n && j < n) {
            if (arr[i] <= dep[j]) {
                currentPlatforms++; // train arrives
                maxPlatforms = max(maxPlatforms, currentPlatforms);
                i++;
            } else {
                currentPlatforms--; // train departs
                j++;
            }
        }
        
        return maxPlatforms;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Independent sorting tracks global concurrent occupancy in a single linear sweep.

---

## 6. Dry Run

`arr = [900, 940, 950, 1100, 1500, 1800]`, `dep = [910, 1200, 1120, 1130, 1900, 2000]`

| Step | Action / State Change | Result |
|---|---|---|
| `Sorted arrays` | arr: `[900, 940, 950, 1100, 1500, 1800]`, dep: `[910, 1120, 1130, 1200, 1900, 2000]` | i=0, j=0 |
| `i=0 (arr 900 <= dep 910)` | platforms = 1, max = 1, i=1 | i=1 |
| `j=0 (arr 940 > dep 910)` | train departs -> platforms = 0, j=1 | j=1 |
| `i=1, 2, 3 (940, 950, 1100 < 1120)` | 3 arrivals -> platforms = 3, max = 3 | max = 3 |
| `Result` | Peak concurrent trains = 3 | Min Platforms = 3 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Same time arrival and departure ($arr[i] == dep[j] \implies$ platform occupied, arrival processed first).
- $N = 1$ (returns 1).

### Common Bugs to Avoid
- Sorting `(arr[i], dep[i])` as paired tuples (must sort `arr` and `dep` arrays INDEPENDENTLY).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is sorting arr and dep independently valid without preserving pairs?**  
  **A**: Because all platforms are identical! When a train leaves, it doesn't matter WHICH train arrived when; ANY train leaving frees up one physical platform, reducing the global count by 1.

- **Q2: Why must arrival take precedence over departure when arr[i] == dep[j]?**  
  **A**: Because the arriving train cannot use the platform at the exact same minute another train is departing. Departure must complete before platform is free.

- **Q3: Can this be solved in O(N) using a difference array for 24-hour time?**  
  **A**: Yes! Since military time is bounded by $[0000, 2359]$ (2360 minutes), use a size 2361 direct frequency array `diff[2361]` with prefix sum sweep in $\mathcal{O}(N + 2360) = \mathcal{O}(N)$ time.


---

## 9. Tags & Related Problems

- **Tags**: `Greedy`, `Two Pointers`, `Sorting`, `Medium`
- **Related problems to practice next**:
- **N Meetings in One Room**: Single room.
- **Meeting Rooms II**: Identical LeetCode problem.
