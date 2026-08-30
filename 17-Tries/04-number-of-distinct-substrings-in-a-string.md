# Number of Distinct Substrings in a String (Trie based O(N^2)) (Step 17.1 — Theory & Practice)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Number of Distinct Substrings in a String (Trie based O(N^2))](https://takeuforward.org/data-structure/number-of-distinct-substrings-in-a-string-using-trie/)
- **Difficulty**: Medium
- **Statement**: Given a string of length $N$ of lowercase alphabet characters. The task is to complete the function `countDistinctSubstring` which returns the count of total number of distinct substrings of this string (including the empty substring if requested, or non-empty distinct substrings).

---

## 1. Problem, Restated

Count all unique contiguous substrings of string `s` by inserting all suffix prefixes into a Trie, counting total newly allocated nodes in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N^2 \times 26)$ space.

- **Input**: String `s` of length $N$.
- **Output**: Total count of distinct substrings.
- **Complexity Goal**: $\mathcal{O}(N^2)$ time and space.

---

## 2. Intuition & Pattern

**The Fundamental Substring-Prefix Equivalence**: 
Every substring of string $s$ is a **prefix of some suffix of $s$**! 
1) If we insert all $N$ suffixes ($s[0 \dots N-1]$, $s[1 \dots N-1]$, ..., $s[N-1]$) into a Trie: 
   - Every distinct path from the `root` down to any node represents a **UNIQUE SUBSTRING** of $s$! 
2) Therefore: 
   $$\text{Total Distinct Substrings} = \text{Total Distinct Nodes Created in Trie}$$ 
**(Plus 1 if the empty substring `""` is counted)**. 
**Algorithm**: 
- Initialize `cnt = 0` (or `1` for empty string). 
- For each starting index $i \in [0, N - 1]$: 
  - Set `node = root`. 
  - For each character $j \in [i, N - 1]$: 
    - If `!node->containsKey(s[j])`: allocate `new Node()`, connect `node->put(s[j], newNode)`, and increment `cnt++`! 
    - Advance `node = node->get(s[j])`. 
- Return `cnt`. Runs in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N^2 \times 26)$ space with zero hash collisions.

- **Underlying Pattern**: `Suffix-Prefix Trie Insertion / Total Trie Nodes Count Invariant`.

---

## 3. Approach 1 — Naive (Hash Set Substring Generation)

### Idea
Generate all $N^2$ substrings and insert them into a `std::unordered_set<string>` in $\mathcal{O}(N^3)$ time.

### C++17 Code
```cpp
#include <string>
#include <unordered_set>
using namespace std;

class SolutionNaive {
public:
    int countDistinctSubstring(string s) {
        unordered_set<string> distinctSubs;
        int n = s.size();
        for (int i = 0; i < n; i++) {
            string current = "";
            for (int j = i; j < n; j++) {
                current += s[j]; // O(N) string copy
                distinctSubs.insert(current); // O(N) hash check
            }
        }
        return distinctSubs.size() + 1; // +1 for empty substring
    }
};
```

### Java Code
```java
import java.util.*;

class SolutionNaive {

    int countDistinctSubstring(String s) {
        Set<String> distinctSubs = new HashSet<>();
        int n = s.length;
        for (int i = 0; i < n; i++) {
            String current = "";
            for (int j = i; j < n; j++) {
                current += s[j]; // O(N) String copy
                distinctSubs.add(current); // O(N) hash check
            }
        }
        return distinctSubs.length + 1; // +1 for empty substring
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^3)$ time ($N^2$ substrings $\times \mathcal{O}(N)$ string copy and hashing).
- **Space Complexity**: $\mathcal{O}(N^3)$ space (storing $N^2$ substrings of average length $N/2$).
- **Why it's not good enough**: Quadratic substring generation with linear memory allocation per substring causes Memory Limit Exceeded.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Suffix Trie below directly achieves optimal $\mathcal{O}(N^2)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Suffix Trie Node Counting)

### Idea
Suffix Trie Insertion with Running Node Allocation Counter in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N^2)$ space.

### C++17 Code
```cpp
#include <string>
#include <vector>
using namespace std;

// Trie Node Definition
struct Node {
    Node* links[26];
    
    bool containsKey(char ch) {
        return links[ch - 'a'] != nullptr;
    }
    
    Node* get(char ch) {
        return links[ch - 'a'];
    }
    
    void put(char ch, Node* node) {
        links[ch - 'a'] = node;
    }
};

class Solution {
public:
    int countDistinctSubstring(string s) {
        int n = s.size();
        Node* root = new Node();
        
        // Count of distinct substrings (initialize to 1 if empty string is included, else 0)
        // Standard GFG problem includes empty string: +1
        int count = 0;
        
        // Insert all suffixes s[i ... n-1] into the Trie
        for (int i = 0; i < n; i++) {
            Node* node = root;
            for (int j = i; j < n; j++) {
                char ch = s[j];
                
                // If character path does not exist, this represents a NEW DISTINCT SUBSTRING!
                if (!node->containsKey(ch)) {
                    count++;
                    node->put(ch, new Node());
                }
                
                node = node->get(ch);
            }
        }
        
        // Return count + 1 (including the empty substring "")
        return count + 1;
    }
};
```

### Java Code
```java
// Trie Node Definition
static class Node {
    Node  links[26];
    
    boolean containsKey(char ch) {
        return links[ch - 'a'] != null;
    }
    
    Node  get(char ch) {
        return links[ch - 'a'];
    }
    
    void put(char ch, Node  node) {
        links[ch - 'a'] = node;
    }
};

class Solution {

    int countDistinctSubstring(String s) {
        int n = s.length;
        Node  root = new Node();
        
        // Count of distinct substrings (initialize to 1 if empty String is included, else 0)
        // Standard GFG problem includes empty String: +1
        int count = 0;
        
        // Insert all suffixes s[i ... n-1] into the Trie
        for (int i = 0; i < n; i++) {
            Node  node = root;
            for (int j = i; j < n; j++) {
                char ch = s[j];
                
                // If character path does not exist, this represents a NEW DISTINCT SUBSTRING!
                if (!node.containsKey(ch)) {
                    count++;
                    node.put(ch, new Node());
                }
                
                node = node.get(ch);
            }
        }
        
        // Return count + 1 (including the empty substring "")
        return count + 1;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time (two nested loops running in total $N(N+1)/2$ iterations, each taking $\mathcal{O}(1)$).
- **Space Complexity**: $\mathcal{O}(N^2 \times 26)$ worst-case space (when all characters are unique, at most $N(N+1)/2$ nodes).
- **Why this is optimal**: Node allocation counter computes distinct substrings directly in $\mathcal{O}(1)$ per character without any string concatenation.

---

## 6. Dry Run

`s = "abab"` ($N = 4$)

| Step | Action / State Change | Result |
|---|---|---|
| `i = 0 (suffix "abab")` | j=0 ('a'): new node $\to cnt=1$; j=1 ('b'): new node $\to cnt=2$; j=2 ('a'): new node $\to cnt=3$; j=3 ('b'): new node $\to cnt=4$ | Substrings: "a", "ab", "aba", "abab" (cnt=4) |
| `i = 1 (suffix "bab")` | j=1 ('b'): new node $\to cnt=5$; j=2 ('a'): new node $\to cnt=6$; j=3 ('b'): new node $\to cnt=7$ | Substrings: "b", "ba", "bab" (cnt=7) |
| `i = 2 (suffix "ab")` | j=2 ('a'): already exists! j=3 ('b'): already exists! $\to$ 0 new nodes | Duplicates "a", "ab" skipped! |
| `i = 3 (suffix "b")` | j=3 ('b'): already exists! $\to$ 0 new nodes | Duplicate "b" skipped! |
| `Result` | Distinct substrings = `cnt + 1` = $7 + 1 = 8$ (`""`, `"a"`, `"ab"`, `"aba"`, `"abab"`, `"b"`, `"ba"`, `"bab"`) | Output = 8 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- All identical characters `"aaaa"` (creates exactly $N$ nodes $\implies N + 1$ distinct substrings).
- All distinct characters `"abcdef"` (creates $N(N+1)/2$ nodes).
- Empty string `""` (returns 1).

### Common Bugs to Avoid
- Forgetting `+ 1` if problem specification includes empty string `""` as a valid distinct substring.
- String copying inside loops (e.g. `s.substr(i, j)`) which reintroduces $\mathcal{O}(N^3)$ TLE.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Can we count distinct substrings in linear O(N) time?**  
  **A**: YES! Using a **Suffix Automaton (SAM)** or **Suffix Tree (Ukkonen's Algorithm)**, the number of distinct substrings can be computed in strictly linear $\mathcal{O}(N)$ time and $\mathcal{O}(N)$ space! In SAM, the answer is $\sum (\text{len}[u] - \text{len}[\text{link}[u]])$ across all states!

- **Q2: How does Suffix Array + LCP solve this in O(N log N) time?**  
  **A**: Build the Suffix Array and LCP (Longest Common Prefix) array using Kasai's algorithm in $\mathcal{O}(N \log N)$. The total distinct substrings is $\frac{N(N+1)}{2} - \sum_{i=1}^{N-1} \text{LCP}[i]$!

- **Q3: What is the memory advantage of Trie over Suffix Automaton for small N (N <= 2000)?**  
  **A**: For $N \le 2000$, a 26-way pointer Trie is extremely simple to implement in under 30 lines of clean C++ without complex link pointer maintenance.


---

## 9. Tags & Related Problems

- **Tags**: `Trie`, `Strings`, `Distinct Substrings`, `Suffix Tree`, `Medium`
- **Related problems to practice next**:
- **Implement Trie I**: Base structure.
- **Longest Common Substring**: Matching substring DP.
