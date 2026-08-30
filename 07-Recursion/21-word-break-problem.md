# Word Break Problem (Backtracking / Trie / DP) (Step 7.3 — Hard Recursion Problems & Backtracking)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Word Break Problem (Backtracking / Trie / DP)](https://takeuforward.org/data-structure/word-break-problem/)
- **Difficulty**: Hard
- **Statement**: Given a string $s$ and a dictionary of strings `wordDict`, return `true` if $s$ can be segmented into a space-separated sequence of one or more dictionary words.

---

## 1. Problem, Restated

Determine if string $s$ can be partitioned into valid dictionary words using memoized backtracking.

- **Input**: Grid / String / Constraints.
- **Output**: Boolean feasibility or complete vector of solution configurations.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

At index `start`: loop `end` from `start` to `s.length() - 1`. If `wordDict` contains prefix `s[start..end]`, recursively check suffix `canBreak(end + 1)`. Memoize results in `memo[start]` to eliminate exponential repeated subproblems.

- **Underlying Pattern**: `Prefix Dictionary Matching + Memoized Backtracking`.

---

## 3. Approach 1 — Naive / Unpruned Search

### Idea
Naive recursion without memoization in $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
// O(2^N) recursion without memo
```

### Java Code
```java
// Java equivalent
// O(2^N) recursion without memo
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Repeated overlapping subproblems.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard backtracking algorithm below directly provides optimal pruning.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
Memoized Backtracking with Unordered Set in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
#include <string>
#include <vector>
#include <unordered_set>
using namespace std;

class Solution {
private:
    bool backtrack(int start, const string& s, const unordered_set<string>& dict, vector<int>& memo) {
        if (start == (int)s.length()) return true;
        if (memo[start] != -1) return memo[start];
        
        string prefix = "";
        for (int i = start; i < (int)s.length(); i++) {
            prefix += s[i];
            if (dict.count(prefix) && backtrack(i + 1, s, dict, memo)) {
                return memo[start] = 1;
            }
        }
        
        return memo[start] = 0;
    }

public:
    bool wordBreak(string s, vector<string>& wordDict) {
        unordered_set<string> dict(wordDict.begin(), wordDict.end());
        vector<int> memo(s.length(), -1);
        return backtrack(0, s, dict, memo);
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    boolean backtrack(int start, String s, Set<String> dict, int[] memo) {
        if (start == s.length()) return true;
        if (memo[start] != -1) return memo[start];
        
        String prefix = "";
        for (int i = start; i < s.length(); i++) {
            prefix += s[i];
            if (dict.contains(prefix) && backtrack(i + 1, s, dict, memo)) {
                return memo[start] = 1;
            }
        }
        
        return memo[start] = 0;
    }

    boolean wordBreak(String s, String[] wordDict) {
        Set<String> dict(wordDict.begin(), wordDict.end());
        int[] memo(s.length(), -1);
        return backtrack(0, s, dict, memo);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time (each of the $N$ states explored in at most $N$ prefix checks).
- **Space Complexity**: $\mathcal{O}(N)$ memoization table + recursion stack.
- **Why this is optimal**: Memoization reduces exponential state tree to polynomial time.

---

## 6. Dry Run

$s = \text{"leetcode"}, wordDict = ["leet", "code"]$

| Step | Action / State Change | Result |
|---|---|---|
| `start = 0` | prefix "leet" is in dict -> recurse(start=4) | Valid prefix |
| `start = 4` | prefix "code" is in dict -> recurse(start=8) | Valid prefix |
| `start = 8` | start == length -> return true | Return TRUE ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single character matching/mismatching.
- Dictionary words can be reused multiple times.

### Common Bugs to Avoid
- Omitting memoization (triggers TLE on `"aaaaaaab"` with `["a", "aa", "aaa"]`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How to optimize dictionary lookup for long words?**  
  **A**: Insert all dictionary words into a **Trie**. Traverse the Trie character-by-character along with string $s$, eliminating substring creation.


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Backtracking`, `Dynamic Programming`, `LeetCode-139`, `Hard`
- **Related problems to practice next**:
- **Palindrome Partitioning**: Partition pattern.
- **Trie**: Trie word search.
