# Book Allocation Problem (Step 4.2 — BS on Answers)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Book Allocation Problem](https://takeuforward.org/data-structure/allocate-minimum-number-of-pages/)
- **Difficulty**: Hard
- **Statement**: Given an array `arr` of $n$ books where `arr[i]` is the number of pages in the $i$-th book, and an integer $m$ representing the number of students. Allocate all books to $m$ students such that: 1) Each book is allocated to exactly one student, 2) Each student gets at least one book, 3) Allocation is contiguous, 4) The maximum number of pages assigned to any student is minimized. Return the minimum possible value of the maximum pages. If allocation is impossible ($m > n$), return `-1`.

---

## 1. Problem, Restated

Split the array into at most $m$ contiguous subarrays such that the maximum sum among all subarrays is as small as possible.

- **Input**: Vector of integers `arr`/`stalls`/`nums`, plus query parameters.
- **Output**: Minimized or maximized value meeting the specification.
- **Constraints**: $1 \le n \le 10^5$, values fit in 32/64-bit integer ranges.

---

## 2. Intuition & Pattern

The search space for maximum pages $P$ is bounded by `low = max(arr)` (a student must read at least the largest single book) and `high = sum(arr)` (one student reads everything). As $P$ increases, the number of students required monotonically decreases: `[false, false, ..., false, true, true, ...]`. We binary search to find the **FIRST `true`**.

- **Underlying Pattern**: `Minimise the Maximum Pages (BS on Answer Range)`.
- **The "Aha!" Moment**: Recognizing how the feasibility predicate divides the answer domain into monotonic boolean halves.

---

## 3. Approach 1 — Brute Force (Linear Range Scan)

### Idea
Linear Scan: Iterate max pages $P$ from $\max(\text{arr})$ to $\sum \text{arr}$. Count students needed. Return first $P$ with $\text{students} \le m$.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <numeric>
using namespace std;

int countStudents(const vector<int>& arr, int maxPages) {
    int students = 1, pagesStudent = 0;
    for (int p : arr) {
        if (pagesStudent + p > maxPages) {
            students++;
            pagesStudent = p;
        } else {
            pagesStudent += p;
        }
    }
    return students;
}

int findPagesLinear(vector<int>& arr, int n, int m) {
    if (m > n) return -1;
    int low = *max_element(arr.begin(), arr.end());
    int high = accumulate(arr.begin(), arr.end(), 0);
    for (int pages = low; pages <= high; pages++) {
        if (countStudents(arr, pages) <= m) return pages;
    }
    return low;
}
```

### Java Code
```java
class Solution {
    int countStudents(int[] arr, int maxPages) {
        int students = 1, pagesStudent = 0;
        for (int p : arr) {
            if (pagesStudent + p > maxPages) {
                students++;
                pagesStudent = p;
            } else {
                pagesStudent += p;
            }
        }
        return students;
    }
    
    int findPagesLinear(int[] arr, int n, int m) {
        if (m > n) return -1;
        int low = max_element(arr.begin(), arr.end());
        int high = accumulate(arr.begin(), arr.end(), 0);
        for (int pages = low; pages <= high; pages++) {
            if (countStudents(arr, pages) <= m) return pages;
        }
        return low;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}((\sum - \max) \times n)$ — linear range scan causes TLE.
- **Space Complexity**: $\mathcal{O}(1)$ space.
- **Why it's not good enough**: Testing every page threshold sequentially is too slow for large sums.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal (Binary Search on Answer Space)

### Idea
Binary Search on Page Capacity: `low = *max_element(arr), high = accumulate(arr)`. Compute `mid`. If `countStudents(arr, mid) <= m`, record `ans = mid, high = mid - 1` (minimize maximum pages); else `low = mid + 1`.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <numeric>
using namespace std;

class Solution {
private:
    int countStudents(const vector<int>& arr, int maxPages) {
        int students = 1;
        long long pagesStudent = 0;
        
        for (int p : arr) {
            if (pagesStudent + p > maxPages) {
                students++;
                pagesStudent = p;
            } else {
                pagesStudent += p;
            }
        }
        return students;
    }

public:
    int findPages(vector<int>& arr, int n, int m) {
        // Book allocation is impossible if students exceed books
        if (m > n) return -1;
        
        int low = *max_element(arr.begin(), arr.end());
        long long high = accumulate(arr.begin(), arr.end(), 0LL);
        int ans = -1;
        
        while (low <= high) {
            long long mid = low + (high - low) / 2;
            
            if (countStudents(arr, mid) <= m) {
                ans = mid;        // feasible, try to find smaller maximum
                high = mid - 1;
            } else {
                low = mid + 1;    // too many students needed, increase page capacity
            }
        }
        
        return ans; // or low at termination
    }
};
```

### Java Code
```java
class Solution {

    int countStudents(int[] arr, int maxPages) {
        int students = 1;
        long pagesStudent = 0;
        
        for (int p : arr) {
            if (pagesStudent + p > maxPages) {
                students++;
                pagesStudent = p;
            } else {
                pagesStudent += p;
            }
        }
        return students;
    }

    int findPages(int[] arr, int n, int m) {
        // Book allocation is impossible if students exceed books
        if (m > n) return -1;
        
        int low = max_element(arr.begin(), arr.end());
        long high = accumulate(arr.begin(), arr.end(), 0LL);
        int ans = -1;
        
        while (low <= high) {
            long mid = low + (high - low) / 2;
            
            if (countStudents(arr, mid) <= m) {
                ans = mid;        // feasible, try to find smaller maximum
                high = mid - 1;
            } else {
                low = mid + 1;    // too many students needed, increase page capacity
            }
        }
        
        return ans; // or low at termination
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n \log_2(\sum \text{pages}))$ — binary search takes $\approx 32$ iterations of $\mathcal{O}(n)$ checks.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Information-theoretic lower bound for minimax contiguous partition.

---

## 6. Dry Run

`arr = [12, 34, 67, 90]`, $n = 4, m = 2$. `low = 90, high = 203`

| Step | Action / State Change | Result |
|---|---|---|
| `Iter 1` | mid = 146. S1: [12, 34, 67] (113), S2: [90] (90) -> 2 students | students=2 <= 2 -> ans = 146, high = 145 |
| `Iter 2` | mid = 117. S1: [12, 34, 67] (113), S2: [90] (90) -> 2 students | students=2 <= 2 -> ans = 117, high = 116 |
| `Iter 3` | mid = 103. S1: [12, 34] (46), S2: [67] (67), S3: [90] (90) -> 3 students | students=3 > 2 -> low = 104 |
| `Iter 4..6` | converges to low = 113, high = 112 | Final ans = 113 ✅ (S1: 12+34+67=113, S2: 90) |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- m > n (more students than books $\implies$ return -1).
- m == n (each student gets 1 book $\implies$ return $\max(\text{arr})$).
- m == 1 (one student gets all books $\implies$ return $\sum \text{arr}$).

### Common Bugs to Avoid
- Forgetting `if (m > n) return -1;` boundary check.
- Setting `low = 1` or `low = 0` instead of `*max_element(arr)`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is Book Allocation identical to Painter's Partition and Split Array Largest Sum?**  
  **A**: All three problems are exact mathematical isomorphic representations of partitioning an array of $n$ numbers into $K$ contiguous subarrays to minimize the maximum subarray sum.

- **Q2: Why is the greedy partition inside countStudents provably optimal?**  
  **A**: Because elements must remain contiguous, greedily packing as many pages as possible to each student without exceeding `maxPages` leaves the maximum possible remaining books for subsequent students, strictly minimizing total students needed.

- **Q3: What if books can be allocated non-contiguously?**  
  **A**: It becomes the **Multi-Processor Scheduling / Subset Sum** problem, which is **NP-Hard**.

- **Q4: How to prevent 64-bit integer overflow in sum calculation?**  
  **A**: Use `long long high = accumulate(arr.begin(), arr.end(), 0LL)` and `long long mid`.

- **Q5: Can dynamic programming solve this in O(n * m)?**  
  **A**: Yes, DP recurrence $DP[i][k] = \min_j (\max(DP[j][k-1], \text{sum}(j+1..i)))$ takes $\mathcal{O}(n^2 m)$ time, which is strictly inferior to Binary Search $\mathcal{O}(n \log(\sum))$.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Search`, `BS on Answers`, `Greedy`, `Hard`
- **Related problems to practice next**:
- **Painter's Partition Problem**: Exact identical clone.
- **Split Array Largest Sum (LeetCode 410)**: Exact identical clone.
- **Capacity to Ship Packages (LeetCode 1011)**: Same contiguous packing pattern.
