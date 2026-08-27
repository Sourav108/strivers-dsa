# Replace Each Element in Array by its Rank (Step 11.2 — Medium Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Replace Each Element in Array by its Rank](https://takeuforward.org/data-structure/replace-elements-by-its-rank-in-the-array/)
- **Difficulty**: Easy
- **Statement**: Given an array of integers `arr`, replace each element with its rank (rank starts from 1; duplicate values share the same rank).

---

## 1. Problem, Restated

Coordinate compression assigning ranks in $\mathcal{O}(N \log N)$ time.

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Value / Top-K elements / Merged list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Copy `arr` to `sortedArr`, sort it. Iterate through `sortedArr`: map unique elements to an incremental `rank` ($1, 2, 3, \dots$) in `unordered_map<int, int> rankMap`. Replace each `arr[i]` with `rankMap[arr[i]]` in $\mathcal{O}(N \log N)$ time.

- **Underlying Pattern**: `Sort + Unique De-duplication Hash Mapping`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
For every element, count unique smaller elements in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
// O(N^2) brute search
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Quadratic time.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Priority Queue / Heap implementation below directly achieves optimal $\mathcal{O}(N \log K)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Sorted Vector / Min-Heap Coordinate Compression in $\mathcal{O}(N \log N)$ time.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <unordered_map>
using namespace std;

class Solution {
public:
    vector<int> arrayRankTransform(vector<int>& arr) {
        vector<int> sortedArr = arr;
        sort(sortedArr.begin(), sortedArr.end());
        
        unordered_map<int, int> rankMap;
        int rank = 1;
        
        for (int x : sortedArr) {
            if (rankMap.find(x) == rankMap.end()) {
                rankMap[x] = rank++;
            }
        }
        
        vector<int> result(arr.size());
        for (size_t i = 0; i < arr.size(); i++) {
            result[i] = rankMap[arr[i]];
        }
        
        return result;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ map and sorted copy space.
- **Why this is optimal**: Direct coordinate compression.

---

## 6. Dry Run

`arr = [40, 10, 20, 30]`

| Step | Action / State Change | Result |
|---|---|---|
| `Sorted` | `[10, 20, 30, 40]` | Sorted |
| `Rank Map` | `{10:1, 20:2, 30:3, 40:4}` | Ranks assigned |
| `Result` | `[4, 1, 2, 3]` | Transformed ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Empty array `[]` (returns `[]`).
- All duplicate elements `[100, 100, 100]` $\implies$ `[1, 1, 1]`.

### Common Bugs to Avoid
- Incrementing rank on duplicates (e.g. assigning rank 2 to second identical number).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is this technique called Coordinate Compression?**  
  **A**: Because it maps large, sparse numerical coordinates (e.g. $10^9$) into small, dense rank indices ($1, 2, \dots, N$) while preserving relative order.


---

## 9. Tags & Related Problems

- **Tags**: `Heap`, `Sorting`, `Hash Map`, `LeetCode-1331`, `Easy`
- **Related problems to practice next**:
- **Kth Largest Element**: Heap sorting.
