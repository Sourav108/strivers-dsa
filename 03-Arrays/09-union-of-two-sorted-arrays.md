# Union of Two Sorted Arrays (Step 3.1)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: https://takeuforward.org/data-structure/union-of-two-sorted-arrays/
- **Difficulty**: Easy
- **Statement**: Return a sorted array containing the distinct union of elements from two sorted arrays.

---

## 1. Problem, Restated

Return a sorted array containing the distinct union of elements from two sorted arrays.

- **Input**: Vector of integers `nums`.
- **Output**: Result as specified by problem requirements.
- **Key Constraints**: $n$ up to $10^5$, elements can be negative/positive, time limit 1.0s.

---

## 2. Intuition & Pattern

Two Pointers Merge. Traverse both arrays in lockstep, picking the smaller element and appending if distinct.

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

vector<int> unionBrute(const vector<int>& a, const vector<int>& b) {
    set<int> s(a.begin(), a.end());
    s.insert(b.begin(), b.end());
    return vector<int>(s.begin(), s.end());
}
```

### Java Code
```java
import java.util.*;

class Solution {
    int[] unionBrute(int[] a, int[] b) {
        Set<Integer> s(a.begin(), a.end());
        s.add(b.begin(), b.end());
        return int[](s.begin(), s.end());
    }
}
```

### Complexity Derivation
- **Time Complexity**: O((n+m) log(n+m))
- **Space Complexity**: O(n+m)
- **Why it's not good enough**: Inserting all elements into `std::set` takes $\mathcal{O}((n+m) \log(n+m))$ time and $\mathcal{O}(n+m)$ tree set memory.

---

## 4. Approach 2 — Better

### Idea
Hash Set / Map: Insert all elements from both arrays into an `unordered_set` to deduplicate, then copy into a vector and sort.

### C++17 Code
```cpp
#include <vector>
#include <unordered_set>
#include <algorithm>
using namespace std;

vector<int> unionBetter(const vector<int>& a, const vector<int>& b) {
    unordered_set<int> uniqueElements;
    for (int x : a) uniqueElements.insert(x);
    for (int x : b) uniqueElements.insert(x);
    
    vector<int> res(uniqueElements.begin(), uniqueElements.end());
    sort(res.begin(), res.end());
    return res;
}
```

### Java Code
```java
import java.util.*;

class Solution {
    int[] unionBetter(int[] a, int[] b) {
        Set<Integer> uniqueElements = new HashSet<>();
        for (int x : a) uniqueElements.add(x);
        for (int x : b) uniqueElements.add(x);
        
        int[] res(uniqueElements.begin(), uniqueElements.end());
        Arrays.sort(res);
        return res;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}((n+m) + U \log U)$ where $U \le n+m$ is the count of unique elements.
- **Space Complexity**: $\mathcal{O}(n+m)$ — hash set storage.
- **Why it's still not optimal**: Ignores the fact that both input arrays are ALREADY sorted, incurring unnecessary hashing and re-sorting overhead.

---

## 5. Approach 3 — Optimal

### Idea
Lockstep Two-Pointer Merge: Maintain pointers `i` and `j` for arrays `a` and `b`. Compare `a[i]` and `b[j]`, append the smaller element to result if it's not a duplicate of `res.back()`, and advance the corresponding pointer.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <climits>
#include <unordered_map>
#include <unordered_set>
using namespace std;

vector<int> unionOptimal(const vector<int>& a, const vector<int>& b) {
    vector<int> res;
    int i = 0, j = 0, n = a.size(), m = b.size();
    while (i < n && j < m) {
        if (a[i] <= b[j]) {
            if (res.empty() || res.back() != a[i]) res.push_back(a[i]);
            i++;
        } else {
            if (res.empty() || res.back() != b[j]) res.push_back(b[j]);
            j++;
        }
    }
    while (i < n) {
        if (res.empty() || res.back() != a[i]) res.push_back(a[i]);
        i++;
    }
    while (j < m) {
        if (res.empty() || res.back() != b[j]) res.push_back(b[j]);
        j++;
    }
    return res;
}
```

### Java Code
```java
import java.util.*;

class Solution {
    int[] unionOptimal(int[] a, int[] b) {
        List<Integer> res = new ArrayList<>();
        int i = 0, j = 0, n = a.length, m = b.length;
        while (i < n && j < m) {
            if (a[i] <= b[j]) {
                if (res.isEmpty() || res.peekLast() != a[i]) res.add(a[i]);
                i++;
            } else {
                if (res.isEmpty() || res.peekLast() != b[j]) res.add(b[j]);
                j++;
            }
        }
        while (i < n) {
            if (res.isEmpty() || res.peekLast() != a[i]) res.add(a[i]);
            i++;
        }
        while (j < m) {
            if (res.isEmpty() || res.peekLast() != b[j]) res.add(b[j]);
            j++;
        }
        return res;
    }
}
```

### Complexity Derivation
- **Time Complexity**: O(n + m)
- **Space Complexity**: O(n + m)
- **Why this is optimal**: Exploits the sorted property to compute the distinct union in $\mathcal{O}(n + m)$ linear time with zero hashing or sorting overhead.

---

## 6. Dry Run

`a = [1, 2, 3, 4, 5]`, `b = [2, 3, 4, 4, 5, 6]`

| Step | Action / State Change | Result |
|---|---|---|
| `i=0, j=0` | a[0]=1 < b[0]=2 -> append 1, i=1 | res=[1] |
| `i=1, j=0` | a[1]=2 == b[0]=2 -> append 2, i=2, j=1 | res=[1, 2] |
| `i=2, j=1` | a[2]=3 == b[1]=3 -> append 3, i=3, j=2 | res=[1, 2, 3] |
| `i=3, j=2` | a[3]=4 == b[2]=4 -> append 4, i=4, j=3 | res=[1, 2, 3, 4] |
| `i=4, j=3` | b[3]=4 == res.back() -> skip duplicate, j=4 | res=[1, 2, 3, 4] |
| `i=4, j=4` | a[4]=5 == b[4]=5 -> append 5, i=5, j=5 | res=[1, 2, 3, 4, 5] |
| `Drain b` | b[5]=6 -> append 6, j=6 | Final: [1, 2, 3, 4, 5, 6] ✅ |

## 7. Edge Cases & Common Bugs

### Edge Cases
- One array is empty -> returns distinct elements of the other array.
- Both arrays contain identical elements -> returns single deduplicated copy.
- Arrays have disjoint ranges (`[1, 2]` and `[3, 4]` -> concatenates without gaps).

### Common Bugs to Avoid
- Accessing `res.back()` when `res.empty()`, causing segmentation fault.
- Forgetting to drain remaining elements from the unfinished array after main loop.

## 8. Follow-Up Questions (Interview Style)

- **Q1: How to adapt this algorithm to find the INTERSECTION of two sorted arrays?**  
  **A**: Advance `i` when `a[i] < b[j]`, advance `j` when `b[j] < a[i]`. When `a[i] == b[j]`, append to result (if not duplicate) and advance both `i++` and `j++` in $\mathcal{O}(n + m)$ time.

- **Q2: What if one array has size n = 10^7 and the other has size m = 5?**  
  **A**: Two-pointer takes $\mathcal{O}(n + m) = 10^7$ operations. Instead, iterate through the $m=5$ elements and Binary Search each inside the $10^7$ array in $\mathcal{O}(m \log n) \approx 5 \times 24 \approx 120$ operations — $80,000\times$ faster!

- **Q3: How to find the Symmetric Difference (A XOR B) of two sorted arrays?**  
  **A**: When `a[i] < b[j]`, take `a[i++]`. When `b[j] < a[i]`, take `b[j++]`. When `a[i] == b[j]`, skip both `i++` and `j++` without adding either.

- **Q4: How to handle multi-set union (preserving duplicate counts)?**  
  **A**: If element $x$ appears 3 times in $A$ and 2 times in $B$, append $x$ exactly $\max(3, 2) = 3$ times in union, or $\min(3, 2) = 2$ times in intersection.

- **Q5: How is this used in database query engines (Sort-Merge Join)?**  
  **A**: Relational database query engines use Sort-Merge Join to combine two indexed table columns in $\mathcal{O}(n + m)$ time using the identical two-pointer merge traversal.

## 9. Tags & Related Problems

- **Tags**: `Array`, `TakeUForward`, `Strivers-A2Z`, `Easy`
- **Related problems**:
  - Similar Step 3 Array Problems in the curriculum.
