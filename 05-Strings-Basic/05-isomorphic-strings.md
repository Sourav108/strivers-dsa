# Isomorphic Strings (Step 5.1 — Basic and Easy String Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Isomorphic Strings](https://takeuforward.org/strings/isomorphic-strings/)
- **Difficulty**: Easy
- **Statement**: Two strings $s$ and $t$ are isomorphic if the characters in $s$ can be replaced to get $t$. All occurrences of a character must be replaced with another character while preserving the order of characters. No two characters may map to the same character, but a character may map to itself.

---

## 1. Problem, Restated

Verify if a bijection (one-to-one and onto mapping) exists between characters of $s$ and $t$.

- **Input**: Problem parameters.
- **Output**: Transformed string or calculated integer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

A bijection requires: 1) $s[i] \to t[i]$ is consistent. 2) $t[i] \to s[i]$ is consistent (no two distinct chars map to the same char). We can track the **last seen position** of characters in $s$ and $t$ using two 256-sized integer arrays `m1` and `m2`. If `m1[s[i]] != m2[t[i]]`, the mapping violates bijection.

- **Underlying Pattern**: `Two-Way Character Mapping (Bijection via ASCII Arrays)`.

---

## 3. Approach 1 — Brute Force / Naive

### Idea
Two hash maps `map<char, char>` in $\mathcal{O}(N \log 256)$ time.

### C++17 Code
```cpp
#include <unordered_map>
#include <string>
using namespace std;
bool isIsomorphicMap(string s, string t) {
    if (s.length() != t.length()) return false;
    unordered_map<char, char> m1, m2;
    for (size_t i = 0; i < s.length(); i++) {
        if (m1.count(s[i]) && m1[s[i]] != t[i]) return false;
        if (m2.count(t[i]) && m2[t[i]] != s[i]) return false;
        m1[s[i]] = t[i];
        m2[t[i]] = s[i];
    }
    return true;
}
```

### Java Code
```java
import java.util.*;

class Solution {
    boolean isIsomorphicMap(String s, String t) {
        if (s.length() != t.length()) return false;
        unordered_map<char, char> m1, m2;
        for (int i = 0; i < s.length(); i++) {
            if (m1.contains(s[i]) && m1[s[i]] != t[i]) return false;
            if (m2.contains(t[i]) && m2[t[i]] != s[i]) return false;
            m1[s[i]] = t[i];
            m2[t[i]] = s[i];
        }
        return true;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(256)$ hash map memory.
- **Why it's not good enough**: Hash map overhead.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Dual Last-Seen Position Array (256 integers) in $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <string>
#include <vector>
using namespace std;

class Solution {
public:
    bool isIsomorphic(string s, string t) {
        if (s.length() != t.length()) return false;
        
        // Store last seen index (+1 to distinguish from unvisited 0)
        int mapS[256] = {0};
        int mapT[256] = {0};
        
        for (int i = 0; i < (int)s.length(); i++) {
            unsigned char c1 = s[i];
            unsigned char c2 = t[i];
            
            if (mapS[c1] != mapT[c2]) {
                return false; // mismatch in previous occurrence pattern
            }
            
            mapS[c1] = i + 1;
            mapT[c2] = i + 1;
        }
        
        return true;
    }
};
```

### Java Code
```java
class Solution {

    boolean isIsomorphic(String s, String t) {
        if (s.length() != t.length()) return false;
        
        // Store last seen index (+1 to distinguish from unvisited 0)
        int mapS[256] = {0};
        int mapT[256] = {0};
        
        for (int i = 0; i < s.length(); i++) {
            int c1 = s[i];
            int c2 = t[i];
            
            if (mapS[c1] != mapT[c2]) {
                return false; // mismatch in previous occurrence pattern
            }
            
            mapS[c1] = i + 1;
            mapT[c2] = i + 1;
        }
        
        return true;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space (256-element stack arrays).
- **Why this is optimal**: Position comparison validates bijection in a single pass.

---

## 6. Dry Run

$s = \text{"egg"}, t = \text{"add"}$

| Step | Action / State Change | Result |
|---|---|---|
| `i = 0` | 'e' and 'a': mapS['e']=0, mapT['a']=0 (Equal) | Set mapS['e']=1, mapT['a']=1 |
| `i = 1` | 'g' and 'd': mapS['g']=0, mapT['d']=0 (Equal) | Set mapS['g']=2, mapT['d']=2 |
| `i = 2` | 'g' and 'd': mapS['g']=2, mapT['d']=2 (Equal) | Set mapS['g']=3, mapT['d']=3 |
| `Result` | All characters match position pattern | Isomorphic! ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $s = \text{"badc"}, t = \text{"baba"}$ (one-way mapping fails: 'd' and 'b' both map to 'a' $\implies$ false).
- Different lengths.

### Common Bugs to Avoid
- Using a single map $s \to t$ without verifying reverse mapping $t \to s$.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does position encoding mapS[c1] = i + 1 work?**  
  **A**: By recording the most recent index where each character appeared, both characters must have identical historical appearance timestamps at every step.


---

## 9. Tags & Related Problems

- **Tags**: `Strings`, `Hashing`, `LeetCode-205`, `Easy`
- **Related problems to practice next**:
- **Valid Anagram**: Character frequency.
