# Assign Cookies (Step 12.1 — Easy)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Assign Cookies](https://takeuforward.org/data-structure/assign-cookies/)
- **Difficulty**: Easy
- **Statement**: Assume you are an awesome parent and want to give your children some cookies. Each child $i$ has a greed factor $g[i]$, and each cookie $j$ has a size $s[j]$. If $s[j] \ge g[i]$, we can assign cookie $j$ to child $i$. Maximize the number of satisfied children.

---

## 1. Problem, Restated

Pair smallest possible satisfying cookie with smallest greedy child using two pointers on sorted arrays.

- **Input**: Parameters specified.
- **Output**: Optimal value / boolean / transformed list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

To maximize satisfied children, never waste a large cookie on a child with small greed if a smaller cookie can satisfy them. Sort both greed array $g$ and cookie size array $s$. Use two pointers `i = 0` (child) and `j = 0` (cookie). If $s[j] \ge g[i]$, child is satisfied $\implies$ `i++`. Always advance cookie pointer `j++`. Return `i`.

- **Underlying Pattern**: `Greedy Two-Pointer Smallest Fit Matching`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
For every child, scan cookie array to find smallest unused cookie $\ge g[i]$ with a visited boolean array in $\mathcal{O}(N \times M)$ time.

### C++17 Code
```cpp
#include <vector>
using namespace std;
int findContentChildrenBrute(vector<int>& g, vector<int>& s) {
    int count = 0;
    vector<bool> used(s.size(), false);
    for (int greed : g) {
        int bestIdx = -1;
        for (size_t j = 0; j < s.size(); j++) {
            if (!used[j] && s[j] >= greed) {
                if (bestIdx == -1 || s[j] < s[bestIdx]) bestIdx = j;
            }
        }
        if (bestIdx != -1) { used[bestIdx] = true; count++; }
    }
    return count;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times M)$ time.
- **Space Complexity**: $\mathcal{O}(M)$ used array.
- **Why it's not good enough**: Quadratic un-sorted search.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard greedy strategy below directly achieves optimal $\mathcal{O}(N \log N)$ or $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Two Pointer Greedy Matching on Sorted Arrays in $\mathcal{O}(N \log N + M \log M)$ time.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int findContentChildren(vector<int>& g, vector<int>& s) {
        sort(g.begin(), g.end());
        sort(s.begin(), s.end());
        
        int childPtr = 0, cookiePtr = 0;
        int n = g.size(), m = s.size();
        
        while (childPtr < n && cookiePtr < m) {
            if (s[cookiePtr] >= g[childPtr]) {
                childPtr++; // child satisfied, move to next child
            }
            cookiePtr++; // always consume cookie
        }
        
        return childPtr;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N + M \log M)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Single two-pointer pass after sorting pairs smallest valid items first.

---

## 6. Dry Run

$g = [1, 2, 3], s = [1, 1]$

| Step | Action / State Change | Result |
|---|---|---|
| `Sorted` | g = `[1, 2, 3]`, s = `[1, 1]` | childPtr = 0, cookiePtr = 0 |
| `Step 1` | $s[0]=1 \ge g[0]=1 \implies$ satisfied! childPtr=1, cookiePtr=1 | child 0 content |
| `Step 2` | $s[1]=1 < g[1]=2 \implies$ cookie too small, cookiePtr=2 | child 1 hungry |
| `Exit` | cookiePtr reaches end $m=2$ | Total satisfied = 1 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- No cookies available $s = []$ (returns 0).
- All cookies smaller than minimum greed.

### Common Bugs to Avoid
- Advancing `childPtr` when $s[cookiePtr] < g[childPtr]$ (skips child without satisfying).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is the greedy choice property optimal here?**  
  **A**: Suppose an optimal solution assigns cookie $s_k$ to child $g_i$, but $s_j < s_k$ also satisfies $g_i$. Replacing $s_k$ with $s_j$ preserves feasibility and frees larger cookie $s_k$ for a potentially more greedy child, never reducing total satisfied children.

- **Q2: Can this be solved using Binary Search on cookies?**  
  **A**: Yes! For each child $g[i]$, `std::lower_bound` on a multiset of cookies takes $\mathcal{O}(N \log M)$ time, but two-pointer on sorted vector is faster and cache-optimal.

- **Q3: How to handle streaming cookies where children arrive dynamically?**  
  **A**: Maintain an ordered `std::multiset` of cookie sizes. When child with greed $g$ arrives, query `lower_bound(g)`, consume the found cookie, and erase from multiset in $\mathcal{O}(\log M)$ time.


---

## 9. Tags & Related Problems

- **Tags**: `Greedy`, `Two Pointers`, `Sorting`, `LeetCode-455`, `Easy`
- **Related problems to practice next**:
- **Lemonade Change**: Greedy change.
