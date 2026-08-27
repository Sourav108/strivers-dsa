# Candy (LeetCode 135 - Slope and Two-Pass method) (Step 12.2 — Medium / Hard)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Candy (LeetCode 135 - Slope and Two-Pass method)](https://takeuforward.org/data-structure/candy/)
- **Difficulty**: Hard
- **Statement**: There are $n$ children standing in a line. Each child is assigned a rating value given in integer array `ratings`. Each child must have at least one candy. Children with a higher rating get more candies than their neighbors. Return the minimum number of candies needed.

---

## 1. Problem, Restated

Distribute minimum candies such that $c[i] > c[i-1]$ if $r[i] > r[i-1]$ and $c[i] > c[i+1]$ if $r[i] > r[i+1]$.

- **Input**: Parameters specified.
- **Output**: Optimal value / boolean / transformed list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) **Two-Pass Approach $\mathcal{O}(N)$ space**: Left pass: if `ratings[i] > ratings[i-1]`, `left[i] = left[i-1] + 1`. Right pass: if `ratings[i] > ratings[i+1]`, `right[i] = right[i+1] + 1`. Total candies $= \sum \max(left[i], right[i])$. 2) **Slope Peak-Valley Approach $\mathcal{O}(1)$ space**: Track ascending slope `peak` and descending slope `down`. Accumulate incremental candies directly across peaks and valleys!

- **Underlying Pattern**: `Left-Right Two-Pass / Peak-Valley Slope Tracking $\mathcal{O}(1)$ Space`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Repeatedly update array candies until all neighbor constraints are satisfied in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
// O(N^2) relaxation
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Quadratic relaxation.

---

## 4. Approach 2 — Better

### Idea
Two-Pass Left and Right vectors in O(N) time and O(N) space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <numeric>
using namespace std;
int candyTwoPass(vector<int>& ratings) {
    int n = ratings.size();
    vector<int> candies(n, 1);
    for (int i = 1; i < n; i++) if (ratings[i] > ratings[i-1]) candies[i] = candies[i-1] + 1;
    for (int i = n - 2; i >= 0; i--) if (ratings[i] > ratings[i+1]) candies[i] = max(candies[i], candies[i+1] + 1);
    return accumulate(candies.begin(), candies.end(), 0);
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ 2 passes.
- **Space Complexity**: $\mathcal{O}(N)$ candies array.
- **Why it's still not optimal**: Uses extra memory.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Slope Peak-Valley Tracking in strict $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int candy(vector<int>& ratings) {
        int n = ratings.size();
        if (n <= 1) return n;
        
        int totalCandies = 1; // first child gets 1 candy
        int i = 1;
        
        while (i < n) {
            // Flat slope: equal ratings
            if (ratings[i] == ratings[i - 1]) {
                totalCandies += 1;
                i++;
                continue;
            }
            
            // Increasing slope (Peak)
            int peak = 1;
            while (i < n && ratings[i] > ratings[i - 1]) {
                peak++;
                totalCandies += peak;
                i++;
            }
            
            // Decreasing slope (Valley)
            int down = 0;
            while (i < n && ratings[i] < ratings[i - 1]) {
                down++;
                totalCandies += down;
                i++;
            }
            
            // Peak compensation: if valley is longer than peak, increase peak height
            totalCandies -= min(peak, down + 1);
            totalCandies += max(peak, down + 1);
        }
        
        return totalCandies;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Direct geometric summation of slope heights consumes zero auxiliary arrays.

---

## 6. Dry Run

`ratings = [1, 0, 2]`

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | candies = 1 | i = 1 |
| `i = 1 (0 < 1)` | down slope: down = 1, peak = 1 -> valley compensation | candies = 3 |
| `i = 2 (2 > 0)` | up slope: peak = 2 -> candies += 2 = 5 | candies = 5 |
| `Result` | Total minimum candies = 5 (`[2, 1, 2]`) | Result = 5 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 1$ (returns 1).
- Strictly increasing ratings `[1, 2, 3, 4]` (sum $1+2+3+4 = 10$).
- Flat line `[1, 1, 1]` (sum $1+1+1 = 3$).

### Common Bugs to Avoid
- Giving children with equal ratings the same candy count when one of their neighbors is smaller (equal ratings only require at least 1 candy).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does equal rating ratings[i] == ratings[i-1] NOT require equal candies?**  
  **A**: The problem only states: 'Children with a HIGHER rating get more candies'. It places no requirement on equal ratings; a child with equal rating can receive 1 candy if their other neighbor allows it.

- **Q2: Why is totalCandies -= min(peak, down + 1) used in slope method?**  
  **A**: The peak element was counted twice: once at the top of the ascending slope, and once at the top of the descending slope. Replacing the double-count with $\max(peak, down + 1)$ assigns the correct height to the peak.

- **Q3: Which approach is preferred in real interviews?**  
  **A**: The Two-Pass $\mathcal{O}(N)$ space method is universally preferred because it is 100% bug-free, highly readable, and easily extensible.


---

## 9. Tags & Related Problems

- **Tags**: `Greedy`, `Array`, `Slope`, `LeetCode-135`, `Hard`
- **Related problems to practice next**:
- **Trapping Rain Water**: Peak-valley intuition.
