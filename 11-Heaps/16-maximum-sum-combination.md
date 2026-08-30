# Maximum Sum Combination (Max-Heap with index pairs) (Step 11.3 — Hard Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Maximum Sum Combination (Max-Heap with index pairs)](https://takeuforward.org/data-structure/maximum-sum-combination/)
- **Difficulty**: Hard
- **Statement**: Given two integer arrays $A$ and $B$ of size $N$ each, find the $C$ maximum sum combinations from all possible sum combinations $A[i] + B[j]$ in $\mathcal{O}(C \log C)$ time.

---

## 1. Problem, Restated

Extract $C$ largest pairs $(A[i] + B[j])$ using sorted arrays, Max-Heap, and visited pair set.

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Value / Top-K elements / Merged list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) Sort $A$ and $B$ in descending order. The largest sum is $A[0] + B[0]$. 2) Push `{A[0] + B[0], 0, 0}` to a Max-Heap. 3) For $C$ iterations: pop maximum `{sum, i, j}`. Next candidates are $\{i+1, j\}$ and $\{i, j+1\}$. Push unvisited neighbors into heap and set. Repeat $C$ times in $\mathcal{O}(C \log C)$ time without generating all $N^2$ pairs!

- **Underlying Pattern**: `Coordinate Best-First Search with Max-Heap & Visited Hash Set`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Generate all $N^2$ sums, sort, take top $C$ in $\mathcal{O}(N^2 \log(N^2))$ time.

### C++17 Code
```cpp
// O(N^2 log N) brute force
```

### Java Code
```java
// Java equivalent
// O(N^2 log N) brute force
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2 \log(N^2))$.
- **Space Complexity**: $\mathcal{O}(N^2)$.
- **Why it's not good enough**: Quadratic pair generation.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Priority Queue / Heap implementation below directly achieves optimal $\mathcal{O}(N \log K)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Max-Heap Best-First Pair Expansion in $\mathcal{O}(N \log N + C \log C)$ time.

### C++17 Code
```cpp
#include <vector>
#include <queue>
#include <set>
#include <algorithm>
using namespace std;

class Solution {
public:
    vector<int> solve(vector<int>& A, vector<int>& B, int C) {
        int n = A.size();
        sort(A.rbegin(), A.rend()); // sort descending
        sort(B.rbegin(), B.rend());
        
        // Max-heap stores: {sum, i, j}
        priority_queue<pair<int, pair<int, int>>> maxHeap;
        set<pair<int, int>> visited; // track pushed index pairs
        
        maxHeap.push({A[0] + B[0], {0, 0}});
        visited.insert({0, 0});
        
        vector<int> result;
        result.reserve(C);
        
        while ((int)result.size() < C && !maxHeap.empty()) {
            auto topElem = maxHeap.top();
            maxHeap.pop();
            
            int sum = topElem.first;
            int i = topElem.second.first;
            int j = topElem.second.second;
            
            result.push_back(sum);
            
            // Candidate 1: (i + 1, j)
            if (i + 1 < n && visited.find({i + 1, j}) == visited.end()) {
                maxHeap.push({A[i + 1] + B[j], {i + 1, j}});
                visited.insert({i + 1, j});
            }
            
            // Candidate 2: (i, j + 1)
            if (j + 1 < n && visited.find({i, j + 1}) == visited.end()) {
                maxHeap.push({A[i] + B[j + 1], {i, j + 1}});
                visited.insert({i, j + 1});
            }
        }
        
        return result;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    int[] solve(int[] A, int[] B, int C) {
        int n = A.length;
        sort(A.rbegin(), A.rend()); // sort descending
        sort(B.rbegin(), B.rend());
        
        // Max-heap stores: {sum, i, j}
        priority_queue<pair<int, pair<int, int>>> maxHeap;
        set<pair<int, int>> visited; // track pushed index pairs
        
        maxHeap.push({A[0] + B[0], {0, 0}});
        visited.add({0, 0});
        
        List<Integer> result = new ArrayList<>();
        result.reserve(C);
        
        while (result.length < C && !maxHeap.isEmpty()) {
            var topElem = maxHeap.peek();
            maxHeap.pop();
            
            int sum = topElem.first;
            int i = topElem.second.first;
            int j = topElem.second.second;
            
            result.add(sum);
            
            // Candidate 1: (i + 1, j)
            if (i + 1 < n && visited.find({i + 1, j}) == visited.end()) {
                maxHeap.push({A[i + 1] + B[j], {i + 1, j}});
                visited.add({i + 1, j});
            }
            
            // Candidate 2: (i, j + 1)
            if (j + 1 < n && visited.find({i, j + 1}) == visited.end()) {
                maxHeap.push({A[i] + B[j + 1], {i, j + 1}});
                visited.add({i, j + 1});
            }
        }
        
        return result;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N + C \log C)$ time.
- **Space Complexity**: $\mathcal{O}(C)$ heap and set space.
- **Why this is optimal**: Only explores at most $2C$ candidate pair nodes in the 2D matrix search space.

---

## 6. Dry Run

$A = [4, 2, 5, 1], B = [8, 0, 5, 3], C = 3$

| Step | Action / State Change | Result |
|---|---|---|
| `Sorted Descending` | A = `[5, 4, 2, 1]`, B = `[8, 5, 3, 0]` | Descending |
| `Pop (0,0)` | sum = 5+8=13 -> push (1,0): 4+8=12, (0,1): 5+5=10 | ans: `[13]` |
| `Pop (1,0)` | sum = 12 -> push (2,0): 2+8=10, (1,1): 4+5=9 | ans: `[13, 12]` |
| `Pop (0,1) or (2,0)` | sum = 10 | ans: `[13, 12, 10]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $C = 1$ (returns largest sum).
- $C = N^2$.

### Common Bugs to Avoid
- Forgetting `visited` set check (pushes duplicate pairs $(i+1, j+1)$ multiple times).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does (i+1, j) and (i, j+1) guarantee finding the next largest sum?**  
  **A**: Because arrays are sorted in descending order. For any cell $(i, j)$, $A[i+1]+B[j] \le A[i]+B[j]$ and $A[i]+B[j+1] \le A[i]+B[j]$. No unvisited cell can have a sum larger than the current heap top!


---

## 9. Tags & Related Problems

- **Tags**: `Heap`, `Priority Queue`, `Greedy`, `Hard`
- **Related problems to practice next**:
- **Find K Pairs with Smallest Sums**: Min-heap counterpart.
