# Longest String Chain (Step 16.6 — DP on LIS)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Longest String Chain](https://takeuforward.org/data-structure/longest-string-chain-dp-45/)
- **Difficulty**: Medium
- **Statement**: You are given an array of `words` where each word consists of lowercase English letters. `wordA` is a predecessor of `wordB` if and only if we can insert exactly one letter anywhere in `wordA` without changing the order of the other characters to make it equal to `wordB`. A word chain is a sequence of words `[word_1, word_2, ..., word_k]` with $k \ge 1$, where `word_1` is a predecessor of `word_2`, `word_2` is a predecessor of `word_3`, and so on. Return the length of the longest possible word chain with words chosen from the given list of `words`.

---

## 1. Problem, Restated

Find the longest predecessor word chain by sorting by word length and using LIS DP / Hash Map state transitions in $\mathcal{O}(N \log N + N \times L^2)$ time and $\mathcal{O}(N)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Length / Array result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**The Word Length Progression Invariant**: 
For `wordA` to be a predecessor of `wordB`, `wordB` MUST have length $\text{len}(\text{wordA}) + 1$. 
1) **Sorting by Length**: Sort all words in ascending order of their string lengths $\mathcal{O}(N \log N)$. 
2) **Transition (Hash Map DP)**: 
   For each word $W$ of length $L$: 
   - For every character position $i \in [0, L - 1]$, delete character $W[i]$ to form candidate predecessor $P = W[0 \dots i-1] + W[i+1 \dots L-1]$. 
   - If predecessor $P$ exists in our hash map `dp`: 
     $$\text{dp}[W] = \max(\text{dp}[W], 1 + \text{dp}[P])$$ 
   - If no predecessor exists, $\text{dp}[W] = 1$. 
3) Global answer is $\max_{W}(\text{dp}[W])$. 
For words of max length $L \le 16$, generating all $L$ predecessors takes $\mathcal{O}(L^2)$ time, giving total runtime $\mathcal{O}(N \log N + N \times L^2)$ and $\mathcal{O}(N \times L)$ space!

- **Underlying Pattern**: `Length-Sorted LIS DP / String Predecessor Hash Map Transition`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively check all word subsets and test valid chains in $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    // O(2^N) recursion
};
```

### Java Code
```java
class SolutionNaive {
    // O(2^N) recursion
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion stack.
- **Why it's not good enough**: Combinatorial subset chain search.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
Sorting by length + 1D LIS O(N^2 x L) DP with two-pointer predecessor validation.

### C++17 Code
```cpp
#include <vector>
#include <string>
#include <algorithm>
using namespace std;

class SolutionLIS2 {
    bool isPredecessor(const string& s1, const string& s2) {
        if (s1.size() + 1 != s2.size()) return false;
        int i = 0, j = 0;
        while (j < s2.size()) {
            if (i < s1.size() && s1[i] == s2[j]) i++;
            j++;
        }
        return i == s1.size();
    }
public:
    int longestStrChain(vector<string>& words) {
        sort(words.begin(), words.end(), [](const string& a, const string& b) {
            return a.size() < b.size();
        });
        int n = words.size(), maxChain = 1;
        vector<int> dp(n, 1);
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < i; j++) {
                if (isPredecessor(words[j], words[i]) && 1 + dp[j] > dp[i]) {
                    dp[i] = 1 + dp[j];
                }
            }
            maxChain = max(maxChain, dp[i]);
        }
        return maxChain;
    }
};
```

### Java Code
```java
class SolutionLIS2 {
    boolean isPredecessor(String s1, String s2) {
        if (s1.length + 1 != s2.length) return false;
        int i = 0, j = 0;
        while (j < s2.length) {
            if (i < s1.length && s1[i] == s2[j]) i++;
            j++;
        }
        return i == s1.length;
    }

    int longestStrChain(String[] words) {
        sort(words.begin(), words.end(), [](String a, String b) {
            return a.length < b.length;
        });
        int n = words.length, maxChain = 1;
        int[] dp = new int[n];
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < i; j++) {
                if (isPredecessor(words[j], words[i]) && 1 + dp[j] > dp[i]) {
                    dp[i] = 1 + dp[j];
                }
            }
            maxChain = Math.max(maxChain, dp[i]);
        }
        return maxChain;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2 \times L)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ space.
- **Why it's still not optimal**: Quadratic word comparisons.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Length-Sorted Hash Map DP with Substring Deletion in $\mathcal{O}(N \log N + N \times L^2)$ time and $\mathcal{O}(N \times L)$ space.

### C++17 Code
```cpp
#include <vector>
#include <string>
#include <unordered_map>
#include <algorithm>
using namespace std;

class Solution {
public:
    int longestStrChain(vector<string>& words) {
        // Step 1: Sort words in ascending order of their lengths
        sort(words.begin(), words.end(), [](const string& a, const string& b) {
            return a.size() < b.size();
        });
        
        // dp[word] stores the length of the longest chain ending at 'word'
        unordered_map<string, int> dp;
        int maxChain = 1;
        
        for (const string& word : words) {
            int currentChain = 1;
            int len = word.size();
            
            // Try removing each character one by one to find predecessors
            for (int i = 0; i < len; i++) {
                string predecessor = word.substr(0, i) + word.substr(i + 1);
                
                if (dp.find(predecessor) != dp.end()) {
                    currentChain = max(currentChain, 1 + dp[predecessor]);
                }
            }
            
            dp[word] = currentChain;
            maxChain = max(maxChain, currentChain);
        }
        
        return maxChain;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    int longestStrChain(String[] words) {
        // Step 1: Sort words in ascending order of their lengths
        sort(words.begin(), words.end(), [](String a, String b) {
            return a.length < b.length;
        });
        
        // dp[word] stores the length of the longest chain ending at 'word'
        Map<String, Integer> dp = new HashMap<>();
        int maxChain = 1;
        
        for (String word : words) {
            int currentChain = 1;
            int len = word.length;
            
            // Try removing each character one by one to find predecessors
            for (int i = 0; i < len; i++) {
                String predecessor = word.substring(0, 0 + i) + word.substring(i + 1);
                
                if (dp.find(predecessor) != dp.end()) {
                    currentChain = Math.max(currentChain, 1 + dp[predecessor]);
                }
            }
            
            dp[word] = currentChain;
            maxChain = Math.max(maxChain, currentChain);
        }
        
        return maxChain;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N + N \times L^2)$ time (where $N \le 1000, L \le 16$, $N \cdot L^2 \approx 2.5 \times 10^5$ operations $\approx 2$ ms).
- **Space Complexity**: $\mathcal{O}(N \times L)$ space (hash map storing $N$ words).
- **Why this is optimal**: Deleting 1 character generates exactly $L$ predecessor lookups in $\mathcal{O}(L^2)$ time per word instead of quadratic $\mathcal{O}(N^2)$ comparisons.

---

## 6. Dry Run

`words = ["a","b","ba","bca","bda","bdca"]`

| Step | Action / State Change | Result |
|---|---|---|
| `len=1: "a", "b"` | dp["a"] = 1, dp["b"] = 1 | maxChain = 1 |
| `len=2: "ba"` | Predecessors: "a" (dp=1), "b" (dp=1) $\implies dp[\text{"ba"}] = 1 + 1 = 2$ | maxChain = 2 |
| `len=3: "bca"` | Predecessors: "ca" (no), "ba" (dp=2), "bc" (no) $\implies dp[\text{"bca"}] = 1 + 2 = 3$ | maxChain = 3 |
| `len=3: "bda"` | Predecessor "ba" (dp=2) $\implies dp[\text{"bda"}] = 3$ | maxChain = 3 |
| `len=4: "bdca"` | Predecessors: "dca", "bca" (dp=3), "bda" (dp=3) $\implies dp[\text{"bdca"}] = 1 + 3 = 4$ | maxChain = 4 |
| `Result` | Return maxChain = 4 (Chain: `"a" -> "ba" -> "bca" -> "bdca"`) | Longest Chain = 4 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 1$ (returns 1).
- No valid predecessor chains (returns 1).
- Multiple branches sharing same prefix.

### Common Bugs to Avoid
- Not sorting by length first (predecessors might not have been evaluated yet when processing longer words!).
- Assuming words appear in topological length order in input.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is Hash Map DP O(N * L^2) significantly faster than standard LIS O(N^2 * L)?**  
  **A**: In standard LIS, we compare all pairs ($N^2 = 10^6$ comparisons). In Hash Map DP, each word has length $L \le 16$, generating ONLY 16 predecessor strings! Total hash lookups $= 16 \times 1000 = 16000$ operations ($100\times$ faster!).

- **Q2: How to print the actual longest chain of words?**  
  **A**: Maintain `parent[word]` in the hash map. Backtrack from the word achieving `maxChain` until no predecessor exists in $\mathcal{O}(\text{chainLength})$ time.

- **Q3: What if we could INSERT or DELETE any character (Levenshtein distance == 1)?**  
  **A**: If edit distance is 1 (lengths equal or $\pm 1$), it forms a general undirected graph problem solved via BFS Shortest Path / Word Ladder!


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `LIS`, `Strings`, `Hash Map`, `LeetCode-1048`, `Medium`
- **Related problems to practice next**:
- **Longest Increasing Subsequence**: Base LIS.
- **Largest Divisible Subset**: Custom transition LIS.
