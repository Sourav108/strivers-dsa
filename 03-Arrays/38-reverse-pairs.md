# Reverse Pairs (LeetCode 493) (Step 3.3)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: https://takeuforward.org/data-structure/count-reverse-pairs/
- **Difficulty**: Hard
- **Statement**: Count pairs $(i, j)$ such that $i < j$ and $nums[i] > 2 \cdot nums[j]$.

---

## 1. Problem, Restated

Count pairs $(i, j)$ such that $i < j$ and $nums[i] > 2 \cdot nums[j]$.

- **Input**: Vector of integers `nums`.
- **Output**: Result as specified by problem requirements.
- **Key Constraints**: $n$ up to $10^5$, elements can be negative/positive, time limit 1.0s.

---

## 2. Intuition & Pattern

Divide & Conquer (Modified Merge Sort with Two Pointers counting step prior to merge).

- **Underlying Pattern**: Array Manipulation / Mathematical Invariants / Pointers.
- **The "Aha!" Moment**: Recognizing how to avoid redundant work by storing running state or leveraging sorting invariants.

---

## 3. Approach 1 — Brute Force

### Idea
Check all possibilities exhaustively using nested loops.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <climits>
#include <set>
#include <unordered_map>
using namespace std;

int reversePairsBrute(const vector<int>& nums) {
    int cnt = 0, n = nums.size();
    for (int i = 0; i < n; i++)
        for (int j = i + 1; j < n; j++)
            if ((long long)nums[i] > 2LL * nums[j]) cnt++;
    return cnt;
}
```

### Java Code
```java
class Solution {
    int reversePairsBrute(int[] nums) {
        int cnt = 0, n = nums.length;
        for (int i = 0; i < n; i++)
            for (int j = i + 1; j < n; j++)
                if ((long)nums[i] > 2LL * nums[j]) cnt++;
        return cnt;
    }
}
```

### Complexity Derivation
- **Time Complexity**: O(n^2)
- **Space Complexity**: O(1)
- **Why it's not good enough**: Checking $nums[i] > 2 \cdot nums[j]$ with nested loops takes $\mathcal{O}(n^2)$ time.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal

### Idea
Modified Merge Sort with Two-Pointer Counting: Before merging, run `countPairs`: for each $i \in [low, mid]$, advance `right` while `arr[i] > 2LL * arr[right]`, adding `(right - (mid + 1))` to count. Then merge.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <climits>
#include <unordered_map>
#include <unordered_set>
using namespace std;

int countPairs(vector<int>& arr, int low, int mid, int high) {
    int right = mid + 1, count = 0;
    for (int i = low; i <= mid; i++) {
        while (right <= high && (long long)arr[i] > 2LL * arr[right]) right++;
        count += (right - (mid + 1));
    }
    return count;
}

void merge(vector<int>& arr, int low, int mid, int high) {
    vector<int> temp;
    int left = low, right = mid + 1;
    while (left <= mid && right <= high) {
        if (arr[left] <= arr[right]) temp.push_back(arr[left++]);
        else temp.push_back(arr[right++]);
    }
    while (left <= mid) temp.push_back(arr[left++]);
    while (right <= high) temp.push_back(arr[right++]);
    for (int i = low; i <= high; i++) arr[i] = temp[i - low];
}

int mergeSortRP(vector<int>& arr, int low, int high) {
    if (low >= high) return 0;
    int mid = low + (high - low) / 2;
    int cnt = mergeSortRP(arr, low, mid);
    cnt += mergeSortRP(arr, mid + 1, high);
    cnt += countPairs(arr, low, mid, high);
    merge(arr, low, mid, high);
    return cnt;
}

int reversePairs(vector<int>& nums) {
    return mergeSortRP(nums, 0, (int)nums.size() - 1);
}
```

### Java Code
```java
import java.util.*;

class Solution {
    int countPairs(int[] arr, int low, int mid, int high) {
        int right = mid + 1, count = 0;
        for (int i = low; i <= mid; i++) {
            while (right <= high && (long)arr[i] > 2LL * arr[right]) right++;
            count += (right - (mid + 1));
        }
        return count;
    }
    
    void merge(int[] arr, int low, int mid, int high) {
        List<Integer> temp = new ArrayList<>();
        int left = low, right = mid + 1;
        while (left <= mid && right <= high) {
            if (arr[left] <= arr[right]) temp.add(arr[left++]);
            else temp.add(arr[right++]);
        }
        while (left <= mid) temp.add(arr[left++]);
        while (right <= high) temp.add(arr[right++]);
        for (int i = low; i <= high; i++) arr[i] = temp[i - low];
    }
    
    int mergeSortRP(int[] arr, int low, int high) {
        if (low >= high) return 0;
        int mid = low + (high - low) / 2;
        int cnt = mergeSortRP(arr, low, mid);
        cnt += mergeSortRP(arr, mid + 1, high);
        cnt += countPairs(arr, low, mid, high);
        merge(arr, low, mid, high);
        return cnt;
    }
    
    int reversePairs(int[] nums) {
        return mergeSortRP(nums, 0, nums.length - 1);
    }
}
```

### Complexity Derivation
- **Time Complexity**: O(2n log n) = O(n log n)
- **Space Complexity**: O(n)
- **Why this is optimal**: Counts reverse pairs in $\mathcal{O}(n \log n)$ time and $\mathcal{O}(n)$ space.

---

## 6. Dry Run

`nums = [1, 3, 2, 3, 1]`

| Step | Action / State Change | Result |
|---|---|---|
| `Divide` | Left=[1, 3, 2], Right=[3, 1] | split |
| `Sub-counts` | Left produces (3,1)->0, (3,2)->0; Right produces (3,1)->1 | cnt=1 |
| `Cross count` | Left [1, 2, 3] and Right [1, 3]: for 3, 3 > 2*1 -> cnt++ | Total reverse pairs = 2 ✅ |

## 7. Edge Cases & Common Bugs

### Edge Cases
- Values equal to `INT_MAX` -> `2LL * arr[right]` prevents 32-bit overflow.
- Array with all negative numbers.

### Common Bugs to Avoid
- Counting during standard merge instead of in a separate pass before merge, which breaks pointer monotonicity.
- Missing `2LL` 64-bit integer literal.

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why CANNOT we count reverse pairs during the standard merge step like normal inversions?**  
  **A**: Because the reverse pair condition $nums[i] > 2 \cdot nums[j]$ is not monotonic with the merge condition $nums[i] > nums[j]$. Elements merged in standard order would break the pointer invariance, requiring a separate counting pass before merging.

- **Q2: Why is the `countPairs` pass O(n) instead of O(n^2)?**  
  **A**: Because both left and right subarrays are sorted. As pointer `i` moves forward in the left half, pointer `right` in the right half only moves forward monotonically. Pointer `right` never resets, giving $\mathcal{O}(n)$ total steps.

- **Q3: Why is 64-bit `(long long)` casting mandatory for `2LL * nums[right]`?**  
  **A**: If $nums[right] = 1.5 \times 10^9$, $2 \cdot nums[right] = 3 \times 10^9$, which exceeds 32-bit signed `INT_MAX` ($2.14 \times 10^9$) causing integer overflow. `2LL * nums[right]` forces 64-bit arithmetic.

- **Q4: How to solve Reverse Pairs using a Binary Indexed Tree (Fenwick Tree)?**  
  **A**: Collect all values $x$ and $2x$, coordinate compress them into rank array, iterate backwards querying count of elements smaller than $nums[i]/2.0$, and insert $nums[i]$ into BIT in $\mathcal{O}(n \log n)$ time.

- **Q5: What if the condition is `nums[i] > K * nums[j]` for any arbitrary constant K?**  
  **A**: The modified merge sort algorithm works identically for any constant $K > 0$ with zero changes to asymptotic $\mathcal{O}(n \log n)$ complexity.

## 9. Tags & Related Problems

- **Tags**: `Array`, `TakeUForward`, `Strivers-A2Z`, `Hard`
- **Related problems**:
  - Similar Step 3 Array Problems in the curriculum.
