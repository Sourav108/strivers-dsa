# Move Zeroes to End (Step 3.1)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: https://takeuforward.org/data-structure/move-all-zeros-to-the-end-of-the-array/
- **Difficulty**: Easy
- **Statement**: Move all 0s to the end while maintaining the relative order of non-zero elements in-place.

---

## 1. Problem, Restated

Move all 0s to the end while maintaining the relative order of non-zero elements in-place.

- **Input**: Vector of integers `nums`.
- **Output**: Result as specified by problem requirements.
- **Key Constraints**: $n$ up to $10^5$, elements can be negative/positive, time limit 1.0s.

---

## 2. Intuition & Pattern

Two pointers partition. Pointer `j` tracks first zero, iterate `i` and swap whenever nums[i] != 0.

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

void moveZeroesBrute(vector<int>& nums) {
    vector<int> nonZero;
    for (int x : nums) if (x != 0) nonZero.push_back(x);
    for (int i = 0; i < (int)nonZero.size(); i++) nums[i] = nonZero[i];
    for (int i = nonZero.size(); i < (int)nums.size(); i++) nums[i] = 0;
}
```

### Java Code
```java
class Solution {
    void moveZeroesBrute(int[] nums) {
        int[] nonZero;
        for (int x : nums) if (x != 0) nonZero.add(x);
        for (int i = 0; i < nonZero.length; i++) nums[i] = nonZero[i];
        for (int i = nonZero.length; i < nums.length; i++) nums[i] = 0;
    }
}
```

### Complexity Derivation
- **Time Complexity**: O(n)
- **Space Complexity**: O(n)
- **Why it's not good enough**: Copying non-zero elements into an auxiliary array takes $\mathcal{O}(n)$ extra space.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal

### Idea
Two-Pointer Partitioning: Pointer `j` locates the first zero. Traverse `i` from $j + 1$ to $n - 1$. Whenever `nums[i] != 0`, swap `nums[i]` with `nums[j]` and increment `j++`.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <climits>
#include <unordered_map>
#include <unordered_set>
using namespace std;

void moveZeroesOptimal(vector<int>& nums) {
    int j = -1, n = nums.size();
    for (int i = 0; i < n; i++) {
        if (nums[i] == 0) { j = i; break; }
    }
    if (j == -1) return; // No zeroes
    for (int i = j + 1; i < n; i++) {
        if (nums[i] != 0) {
            swap(nums[i], nums[j]);
            j++;
        }
    }
}
```

### Java Code
```java
class Solution {
    void moveZeroesOptimal(int[] nums) {
        int j = -1, n = nums.length;
        for (int i = 0; i < n; i++) {
            if (nums[i] == 0) { j = i; break; }
        }
        if (j == -1) return; // No zeroes
        for (int i = j + 1; i < n; i++) {
            if (nums[i] != 0) {
                int temp = nums[i]; nums[i] = nums[j]; nums[j] = temp;
                j++;
            }
        }
    }
}
```

### Complexity Derivation
- **Time Complexity**: O(n)
- **Space Complexity**: O(1)
- **Why this is optimal**: Maintains relative order and moves zeroes in $\mathcal{O}(n)$ time and strictly $\mathcal{O}(1)$ space.

---

## 6. Dry Run

`nums = [0, 1, 0, 3, 12]`

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | First zero found at j=0 | j=0 |
| `i=1` | nums[1]=1 != 0 -> swap(nums[1], nums[0]) -> [1, 0, 0, 3, 12], j=1 | nums=[1, 0, 0, 3, 12] |
| `i=2` | nums[2]=0 -> no swap | j=1 |
| `i=3` | nums[3]=3 != 0 -> swap(nums[3], nums[1]) -> [1, 3, 0, 0, 12], j=2 | nums=[1, 3, 0, 0, 12] |
| `i=4` | nums[4]=12 != 0 -> swap(nums[4], nums[2]) -> [1, 3, 12, 0, 0], j=3 | Final: [1, 3, 12, 0, 0] ✅ |

## 7. Edge Cases & Common Bugs

### Edge Cases
- Array with no zeroes (`[1, 2, 3]` -> returns without modification).
- Array with all zeroes (`[0, 0, 0]` -> `j` stays at 0, no swaps).
- Zeroes already at the end (`[1, 2, 0, 0]` -> 0 swaps).

### Common Bugs to Avoid
- Swapping when $i == j$, causing redundant memory writes.
- Failing to handle arrays containing no zeroes.

## 8. Follow-Up Questions (Interview Style)

- **Q1: How to minimize the total number of write operations if the array has very few zeroes?**  
  **A**: Only perform `swap(nums[i], nums[j])` when `i != j`. If the array has zero zeroes, `i == j` always and 0 writes occur.

- **Q2: What if we must move zeroes to the BEGINNING instead of the end while maintaining relative order?**  
  **A**: Scan from right to left: initialize `j = n - 1` at the last zero. Iterate `i` from $n - 1$ down to 0, swapping `nums[i]` with `nums[j]` whenever `nums[i] != 0` and decrementing `j`.

- **Q3: How does this problem relate to the partition step of QuickSort?**  
  **A**: It is a binary partition where elements are split based on the predicate `x != 0` vs `x == 0`, identical to Lomuto partitioning with stable preservation of non-zero elements.

- **Q4: What if relative order does NOT matter?**  
  **A**: Use two pointers from opposite ends: `left` at 0, `right` at $n-1$. When `nums[left] == 0`, swap with `nums[right--]`. This takes at most $Z$ writes where $Z$ is the count of zeroes.

- **Q5: How to perform this in an immutable stream / functional pipeline?**  
  **A**: Filter elements into a stream `nums.stream().filter(x -> x != 0)` concatenated with `Stream.generate(() -> 0).limit(zeroCount)`.

## 9. Tags & Related Problems

- **Tags**: `Array`, `TakeUForward`, `Strivers-A2Z`, `Easy`
- **Related problems**:
  - Similar Step 3 Array Problems in the curriculum.
