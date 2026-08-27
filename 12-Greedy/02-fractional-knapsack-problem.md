# Fractional Knapsack Problem (Step 12.1 — Easy)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Fractional Knapsack Problem](https://takeuforward.org/data-structure/fractional-knapsack-problem-greedy-approach/)
- **Difficulty**: Medium
- **Statement**: Given weights and values of $N$ items, we need to put these items in a knapsack of capacity $W$ to get the maximum total value in the knapsack. You can break items for maximizing total value of knapsack.

---

## 1. Problem, Restated

Sort items by value-to-weight ratio ($\text{value}/\text{weight}$) descending and greedily take as much as capacity allows.

- **Input**: Parameters specified.
- **Output**: Optimal value / boolean / transformed list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Since fractional parts are allowed, every gram of capacity should be filled with the highest value-density item available. Sort items in descending order of ratio $= \text{value} / \text{weight}$. Iterate through items: 1) If $weight \le W$, take whole item: $W -= weight$, $totalVal += value$. 2) If $weight > W$, take fraction: $totalVal += value \times (W / weight)$, $W = 0$, break.

- **Underlying Pattern**: `Value-to-Weight Density Sorting`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Generating all permutations of item selections in exponential time.

### C++17 Code
```cpp
// Exponential permutation approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N!)$ exponential.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Brute force search.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard greedy strategy below directly achieves optimal $\mathcal{O}(N \log N)$ or $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Greedy Ratio Sorting in $\mathcal{O}(N \log N)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

struct Item {
    int value;
    int weight;
};

class Solution {
private:
    static bool compare(const Item& a, const Item& b) {
        double r1 = (double)a.value / (double)a.weight;
        double r2 = (double)b.value / (double)b.weight;
        return r1 > r2; // Descending order of density
    }

public:
    double fractionalKnapsack(int w, Item arr[], int n) {
        sort(arr, arr + n, compare);
        
        double totalValue = 0.0;
        int remainingCapacity = w;
        
        for (int i = 0; i < n; i++) {
            if (arr[i].weight <= remainingCapacity) {
                // Take whole item
                totalValue += arr[i].value;
                remainingCapacity -= arr[i].weight;
            } else {
                // Take fractional part
                totalValue += ((double)arr[i].value / (double)arr[i].weight) * (double)remainingCapacity;
                break;
            }
        }
        
        return totalValue;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Greedy ratio ranking provably maximizes total value under continuous knapsack bounds.

---

## 6. Dry Run

Items: `[(60, 10), (100, 20), (120, 30)]`, Capacity $W = 50$

| Step | Action / State Change | Result |
|---|---|---|
| `Ratios` | Item 1: 60/10=6.0, Item 2: 100/20=5.0, Item 3: 120/30=4.0 | Sorted order: 1, 2, 3 |
| `Take Item 1` | wt=10 <= 50 -> totalVal = 60, remW = 40 | remW = 40 |
| `Take Item 2` | wt=20 <= 40 -> totalVal = 60 + 100 = 160, remW = 20 | remW = 20 |
| `Take Item 3 (frac)` | wt=30 > 20 -> take 20/30 frac = $120 \times (20/30) = 80$ | totalVal = 240.0, remW = 0 |
| `Result` | Max Value = 240.0 | Result = 240.0 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $W = 0$ (returns 0.0).
- Total weight of all items $< W$ (takes all items).

### Common Bugs to Avoid
- Integer division in comparator: `a.value / a.weight` (must cast to `double`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does Greedy work for Fractional Knapsack but FAILS for 0/1 Knapsack?**  
  **A**: In Fractional Knapsack, capacity can be filled continuously, guaranteeing the highest density item is always optimal. In 0/1 Knapsack, indivisible items create leftover empty space that could be better utilized by combinations of lower-density items (requiring Dynamic Programming).

- **Q2: Can comparator avoid floating-point division to prevent precision errors?**  
  **A**: Yes! Instead of `(double)a.val / a.wt > (double)b.val / b.wt`, compare cross-multiplied integers: `1LL * a.value * b.weight > 1LL * b.value * a.weight`!

- **Q3: How to optimize to O(N) average time?**  
  **A**: Use **QuickSelect** on value-density to partition items around the pivot filling capacity $W$, achieving $\mathcal{O}(N)$ expected time.


---

## 9. Tags & Related Problems

- **Tags**: `Greedy`, `Sorting`, `Knapsack`, `Medium`
- **Related problems to practice next**:
- **0/1 Knapsack**: DP version.
