# Leaders in an Array (Step 3.2)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: https://takeuforward.org/data-structure/leaders-in-an-array/
- **Difficulty**: Easy
- **Statement**: An element is a leader if it is strictly greater than all elements to its right.

---

## 1. Problem, Restated

An element is a leader if it is strictly greater than all elements to its right.

- **Input**: Vector of integers `nums`.
- **Output**: Result as specified by problem requirements.
- **Key Constraints**: $n$ up to $10^5$, elements can be negative/positive, time limit 1.0s.

---

## 2. Intuition & Pattern

Right-to-Left Running Maximum Scan.

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

vector<int> leadersBrute(const vector<int>& nums) {
    vector<int> res; int n = nums.size();
    for (int i = 0; i < n; i++) {
        bool leader = true;
        for (int j = i + 1; j < n; j++) if (nums[j] >= nums[i]) { leader = false; break; }
        if (leader) res.push_back(nums[i]);
    }
    return res;
}
```

### Java Code
```java
import java.util.*;

class Solution {
    int[] leadersBrute(int[] nums) {
        List<Integer> res = new ArrayList<>(); int n = nums.length;
        for (int i = 0; i < n; i++) {
            boolean leader = true;
            for (int j = i + 1; j < n; j++) if (nums[j] >= nums[i]) { leader = false; break; }
            if (leader) res.add(nums[i]);
        }
        return res;
    }
}
```

### Complexity Derivation
- **Time Complexity**: O(n^2)
- **Space Complexity**: O(1)
- **Why it's not good enough**: Nested loops checking all elements to the right for every index takes $\mathcal{O}(n^2)$ time.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal

### Idea
Right-to-Left Running Maximum: Traverse from $n-1$ down to 0 maintaining `maxFromRight = INT_MIN`. If `nums[i] > maxFromRight`, push `nums[i]` to `res` and update `maxFromRight = nums[i]`. Reverse `res` before returning.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <climits>
#include <unordered_map>
#include <unordered_set>
using namespace std;

vector<int> leadersOptimal(const vector<int>& nums) {
    vector<int> res;
    int maxFromRight = INT_MIN, n = nums.size();
    for (int i = n - 1; i >= 0; i--) {
        if (nums[i] > maxFromRight) {
            res.push_back(nums[i]);
            maxFromRight = nums[i];
        }
    }
    reverse(res.begin(), res.end());
    return res;
}
```

### Java Code
```java
import java.util.*;

class Solution {
    int[] leadersOptimal(int[] nums) {
        List<Integer> res = new ArrayList<>();
        int maxFromRight = Integer.MIN_VALUE, n = nums.length;
        for (int i = n - 1; i >= 0; i--) {
            if (nums[i] > maxFromRight) {
                res.add(nums[i]);
                maxFromRight = nums[i];
            }
        }
        reverse(res.begin(), res.end());
        return res;
    }
}
```

### Complexity Derivation
- **Time Complexity**: O(n)
- **Space Complexity**: O(1)
- **Why this is optimal**: Single-pass backward scan finds all leaders in $\mathcal{O}(n)$ time and $\mathcal{O}(1)$ auxiliary space.

---

## 6. Dry Run

`nums = [16, 17, 4, 3, 5, 2]`

| Step | Action / State Change | Result |
|---|---|---|
| `i=5 (x=2)` | 2 > INT_MIN -> leader 2, max=2 | res=[2] |
| `i=4 (x=5)` | 5 > 2 -> leader 5, max=5 | res=[2, 5] |
| `i=3 (x=3)` | 3 < 5 -> not leader | max=5 |
| `i=2 (x=4)` | 4 < 5 -> not leader | max=5 |
| `i=1 (x=17)` | 17 > 5 -> leader 17, max=17 | res=[2, 5, 17] |
| `i=0 (x=16)` | 16 < 17 -> not leader | res=[2, 5, 17] |
| `Reverse` | reverse(res) | Final Leaders: `[17, 5, 2]` ✅ |

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single element array (`[10]` -> returns `[10]`).
- Strictly increasing array (`[1, 2, 3, 4]` -> only last element `[4]` is leader).
- Strictly decreasing array (`[4, 3, 2, 1]` -> all elements are leaders).

### Common Bugs to Avoid
- Forgetting to reverse `res` to match the original left-to-right order.

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is scanning right-to-left O(n) while scanning left-to-right is O(n^2)?**  
  **A**: Scanning right-to-left maintains the maximum of the suffix in $\mathcal{O}(1)$ time per step. Scanning left-to-right requires re-scanning the entire remaining right suffix for every element.

- **Q2: How to return leaders in their original left-to-right order without calling `reverse()`?**  
  **A**: Store leaders in an `std::vector` and reverse it, or push into an `std::stack` and pop to output, or prepend to a Singly Linked List in $\mathcal{O}(1)$ per insertion.

- **Q3: How does the Leaders problem relate to Monotonic Stacks (Next Greater Element)?**  
  **A**: An element is a leader if and only if its Next Greater Element to the right does not exist. A monotonic decreasing stack naturally identifies all leaders.

- **Q4: What if the definition requires strictly greater (`>`) vs greater than or equal (`>=`)?**  
  **A**: Change `nums[i] > maxFromRight` to `nums[i] >= maxFromRight`. For `[10, 10, 10]`, `>` returns `[10]` whereas `>=` returns `[10, 10, 10]`.

- **Q5: How to find 2D peak / leader elements in an N x M matrix?**  
  **A**: Binary Search on columns: find global maximum of middle column in $\mathcal{O}(M)$, check left and right neighbors, and recurse on the half containing a larger neighbor in $\mathcal{O}(M \log N)$ time.

## 9. Tags & Related Problems

- **Tags**: `Array`, `TakeUForward`, `Strivers-A2Z`, `Easy`
- **Related problems**:
  - Similar Step 3 Array Problems in the curriculum.
