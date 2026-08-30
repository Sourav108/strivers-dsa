# Reverse an Array using Recursion (Step 1.5 — Learn Basic Recursion)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Reverse an Array using Recursion](https://takeuforward.org/data-structure/reverse-a-given-array/)
- **Difficulty**: Easy
- **Statement**: Given an array `arr` of $n$ elements, reverse it in-place using recursion.

---

## 1. Problem, Restated

Swap symmetric elements `arr[i]` and `arr[n - 1 - i]` recursively from ends toward center.

- **Input**: Parameters specified.
- **Output**: Value or side-effect meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Instead of using two pointers $(l, r)$, a single pointer $i$ suffices: the reflected partner of index $i$ is $n - 1 - i$. Base case: when $i \ge n / 2$, the array is fully reversed. Recurrence: swap `arr[i]` with `arr[n-1-i]`, then recurse on $i + 1$.

- **Underlying Pattern**: `Two-Pointer Invariant Recursion: $f(i) \implies \text{swap}(i, n-1-i)$`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Create auxiliary vector and copy in reverse in $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <vector>
using namespace std;
vector<int> reverseExtraSpace(const vector<int>& arr) {
    vector<int> rev;
    for (int i = (int)arr.size() - 1; i >= 0; i--) rev.push_back(arr[i]);
    return rev;
}
```

### Java Code
```java
import java.util.*;

class Solution {
    int[] reverseExtraSpace(int[] arr) {
        List<Integer> rev = new ArrayList<>();
        for (int i = arr.length - 1; i >= 0; i--) rev.add(arr[i]);
        return rev;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ auxiliary memory.
- **Why it's not good enough**: Allocates extra buffer.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
In-place recursive two-pointer swap using single index $i$.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
private:
    void reverseHelper(vector<int>& arr, int i, int n) {
        // Base case: passed midpoint
        if (i >= n / 2) return;
        
        // Swap symmetric endpoints
        swap(arr[i], arr[n - 1 - i]);
        
        // Recurse on next inner pair
        reverseHelper(arr, i + 1, n);
    }

public:
    void reverseArray(vector<int>& arr) {
        reverseHelper(arr, 0, arr.size());
    }
};
```

### Java Code
```java
class Solution {

    void reverseHelper(int[] arr, int i, int n) {
        // Base case: passed midpoint
        if (i >= n / 2) return;
        
        // Swap symmetric endpoints
        int temp = arr[i]; arr[i] = arr[n - 1 - i]; arr[n - 1 - i] = temp;
        
        // Recurse on next inner pair
        reverseHelper(arr, i + 1, n);
    }

    void reverseArray(int[] arr) {
        reverseHelper(arr, 0, arr.length);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N / 2) = \mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N / 2)$ stack frames.
- **Why this is optimal**: In-place reversal with single pointer tracking.

---

## 6. Dry Run

`arr = [1, 2, 3, 4, 5]`, $n = 5$. Midpoint $n/2 = 2$

| Step | Action / State Change | Result |
|---|---|---|
| `$i = 0$` | swap(arr[0], arr[4]) -> swap(1, 5) | `[5, 2, 3, 4, 1]` |
| `$i = 1$` | swap(arr[1], arr[3]) -> swap(2, 4) | `[5, 4, 3, 2, 1]` |
| `$i = 2$` | $i \ge 5/2 (2 \ge 2)$ -> base case returns | Reversal Complete ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 0, 1$ (already reversed)
- Even length array ($N=4$)
- Odd length array ($N=5$, center element stays).

### Common Bugs to Avoid
- Passing vector by value in recursive helper (`vector<int> arr` instead of `vector<int>& arr`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why pass vector by reference?**  
  **A**: Passing by value copies the entire vector on every single recursive call, turning an $\mathcal{O}(N)$ operation into an $\mathcal{O}(N^2)$ disaster.


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Arrays`, `Two Pointers`, `Easy`
- **Related problems to practice next**:
- **Check Palindrome String**: Recursive string symmetry.
