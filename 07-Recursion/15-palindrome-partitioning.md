# Palindrome Partitioning (Step 7.3 — Hard Recursion Problems & Backtracking)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Palindrome Partitioning](https://takeuforward.org/data-structure/palindrome-partitioning/)
- **Difficulty**: Medium
- **Statement**: Given a string $s$, partition $s$ such that every substring of the partition is a palindrome. Return all possible palindrome partitionings of $s$.

---

## 1. Problem, Restated

Find all ways to cut $s$ into contiguous palindromic substrings.

- **Input**: Grid / String / Constraints.
- **Output**: Boolean feasibility or complete vector of solution configurations.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

At index `index`: loop through all partition endpoints `i` from `index` to `n - 1`. If prefix `s[index..i]` is a palindrome: push `s.substr(index, i - index + 1)`, recurse on `i + 1`, backtrack `pop_back()`. When `index == n`, push the partition list to results.

- **Underlying Pattern**: `Prefix Palindrome Validation + Suffix Backtracking`.

---

## 3. Approach 1 — Naive / Unpruned Search

### Idea
Generate all $2^{N-1}$ possible string partitions and validate every substring.

### C++17 Code
```cpp
// Unpruned partition generation
```

### Java Code
```java
// Java equivalent
// Unpruned partition generation
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N \cdot N)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Validates non-palindromic cuts after full partition.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard backtracking algorithm below directly provides optimal pruning.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
Pruned Backtracking: only recurse when the current prefix is a verified palindrome.

### C++17 Code
```cpp
#include <vector>
#include <string>
using namespace std;

class Solution {
private:
    bool isPalindrome(const string& s, int left, int right) {
        while (left < right) {
            if (s[left++] != s[right--]) return false;
        }
        return true;
    }
    
    void partitionHelper(int index, const string& s, vector<string>& path, vector<vector<string>>& ans) {
        if (index == (int)s.length()) {
            ans.push_back(path);
            return;
        }
        
        for (int i = index; i < (int)s.length(); i++) {
            if (isPalindrome(s, index, i)) {
                path.push_back(s.substr(index, i - index + 1));
                partitionHelper(i + 1, s, path, ans);
                path.pop_back(); // backtrack
            }
        }
    }

public:
    vector<vector<string>> partition(string s) {
        vector<vector<string>> ans;
        vector<string> path;
        partitionHelper(0, s, path, ans);
        return ans;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    boolean isPalindrome(String s, int left, int right) {
        while (left < right) {
            if (s[left++] != s[right--]) return false;
        }
        return true;
    }
    
    void partitionHelper(int index, String s, String[] path, List<List<String>> ans) {
        if (index == s.length()) {
            ans.add(path);
            return;
        }
        
        for (int i = index; i < s.length(); i++) {
            if (isPalindrome(s, index, i)) {
                path.add(s.substring(index, index + i - index + 1));
                partitionHelper(i + 1, s, path, ans);
                path.remove(); // backtrack
            }
        }
    }

    List<List<String>> partition(String s) {
        List<List<String>> ans;
        List<String> path = new ArrayList<>();
        partitionHelper(0, s, path, ans);
        return ans;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \cdot 2^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion call stack space.
- **Why this is optimal**: Early pruning cuts non-palindromic branches immediately.

---

## 6. Dry Run

$s = \text{"aab"}$

| Step | Action / State Change | Result |
|---|---|---|
| `Cut 1` | i=0: "a" (pal) -> i=1: "a" (pal) -> i=2: "b" (pal) | `["a", "a", "b"]` |
| `Cut 2` | i=1: "aa" (pal) -> i=2: "b" (pal) | `["aa", "b"]` |
| `Result` | Total partitions | `[["a", "a", "b"], ["aa", "b"]]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single character $s = \text{"a"}$ $\implies$ `[["a"]]`.
- All same characters $s = \text{"aaa"}$ $\implies$ 4 partitions.

### Common Bugs to Avoid
- Calculating substring length incorrectly in `s.substr(index, i - index + 1)`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Can we optimize palindrome checks with DP precomputation?**  
  **A**: Yes! We can precompute a 2D boolean table `isPal[i][j]` in $\mathcal{O}(N^2)$ time to make the palindrome check $\mathcal{O}(1)$ inside the backtracking loop.


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Backtracking`, `Palindrome`, `LeetCode-131`, `Medium`
- **Related problems to practice next**:
- **Palindrome Partitioning II**: DP min cuts.
