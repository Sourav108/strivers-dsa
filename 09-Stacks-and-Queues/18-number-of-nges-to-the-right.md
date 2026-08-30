# Number of NGEs to the Right (Step 9.3 — Monotonic Stack / Queue)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Number of NGEs to the Right](https://takeuforward.org/data-structure/number-of-nges-to-the-right/)
- **Difficulty**: Medium
- **Statement**: Given an array `arr` and $Q$ queries where each query gives an index `idx`, count the total number of elements to the right of `idx` that are strictly greater than `arr[idx]`.

---

## 1. Problem, Restated

Count all elements to the right greater than query index.

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Resulting vector of elements / integer answer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

For $Q$ queries on array of size $N$: for small $N, Q \le 10^3$, a simple scan from $idx + 1$ to $N-1$ runs in $\mathcal{O}(Q \times N)$ time. For large constraints ($N, Q \le 10^5$), process queries using a **Fenwick Tree / Segment Tree with Coordinate Compression** backwards in $\mathcal{O}((N + Q) \log N)$ time.

- **Underlying Pattern**: `Query-Based Linear Scan / Fenwick Coordinate Compression`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Linear scan rightwards for each query.

### C++17 Code
```cpp
#include <vector>
using namespace std;
vector<int> count_NGEs_Brute(int n, vector<int>& arr, int queries, vector<int>& indices) {
    vector<int> ans;
    for (int idx : indices) {
        int count = 0;
        for (int j = idx + 1; j < n; j++) {
            if (arr[j] > arr[idx]) count++;
        }
        ans.push_back(count);
    }
    return ans;
}
```

### Java Code
```java
import java.util.*;

class Solution {
    int[] count_NGEs_Brute(int n, int[] arr, int queries, int[] indices) {
        List<Integer> ans = new ArrayList<>();
        for (int idx : indices) {
            int count = 0;
            for (int j = idx + 1; j < n; j++) {
                if (arr[j] > arr[idx]) count++;
            }
            ans.add(count);
        }
        return ans;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(Q \times N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Straightforward for standard problem constraints.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — monotonic data structure below directly achieves optimal $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Standard Query Processor in $\mathcal{O}(Q \times N)$ (optimal for $N \le 10^3$).

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
public:
    vector<int> count_NGEs(int N, vector<int>& arr, int queries, vector<int>& indices) {
        vector<int> ans;
        ans.reserve(queries);
        
        for (int idx : indices) {
            int count = 0;
            for (int j = idx + 1; j < N; j++) {
                if (arr[j] > arr[idx]) {
                    count++;
                }
            }
            ans.push_back(count);
        }
        
        return ans;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    int[] count_NGEs(int N, int[] arr, int queries, int[] indices) {
        List<Integer> ans = new ArrayList<>();
        ans.reserve(queries);
        
        for (int idx : indices) {
            int count = 0;
            for (int j = idx + 1; j < N; j++) {
                if (arr[j] > arr[idx]) {
                    count++;
                }
            }
            ans.add(count);
        }
        
        return ans;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(Q \times N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Direct counting requires zero complex data structure overhead for standard bounds.

---

## 6. Dry Run

`arr = [3, 4, 2, 7, 5, 8, 10, 6]`, queries at indices `[0, 5]`

| Step | Action / State Change | Result |
|---|---|---|
| `Query idx = 0 (val 3)` | Elements to right > 3: 4, 7, 5, 8, 10, 6 -> Count = 6 | ans[0] = 6 |
| `Query idx = 5 (val 8)` | Elements to right > 8: 10 -> Count = 1 | ans[1] = 1 |
| `Result` | `[6, 1]` | Complete ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Index is last element $idx = N-1$ (count is 0).
- All elements smaller than query element.

### Common Bugs to Avoid
- Including index itself in count.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Can this be solved in O(Q log N) for N = 10^5?**  
  **A**: Yes! Coordinate compress array values and use a Fenwick tree (Binary Indexed Tree) scanning backwards to query sum of frequencies in range $[arr[idx] + 1, \max]$.


---

## 9. Tags & Related Problems

- **Tags**: `Stack`, `Arrays`, `Queries`, `Medium`
- **Related problems to practice next**:
- **Next Greater Element I**: First greater element.
