# Repeated String Match (Rabin-Karp Rolling Hash) (Step 18.1 — String Matching & Hard Algorithms)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Repeated String Match (Rabin-Karp Rolling Hash)](https://takeuforward.org/strings/repeated-string-match/)
- **Difficulty**: Medium
- **Statement**: Given two strings `a` and `b`, return the minimum number of times you should repeat string `a` so that string `b` is a substring of it. If it is impossible for `b` to be a substring of `a` after repeated times, return -1. Notice: string `"abc"` repeated 0 times is `""`, repeated 1 time is `"abc"` and repeated 2 times is `"abcabc"`.

---

## 1. Problem, Restated

Find the minimum integer $k$ such that $b$ is a substring of $a^k$ using Upper Bound Repetition ($\lceil |b|/|a| ceil + 2$) + Rabin-Karp Rolling Hash in $\mathcal{O}(N + M)$ time and $\mathcal{O}(N + M)$ space.

- **Input**: Strings `a` and `b`.
- **Output**: Minimum repetitions integer or -1.
- **Complexity Goal**: $\mathcal{O}(N + M)$ time and space.

---

## 2. Intuition & Pattern

**The Upper Bound Repetition Invariant**: 
Let $N = |a|$ and $M = |b|$. 
1) For $b$ to possibly be a substring of $a^k$, the length of $a^k$ must be at least $M$: 
   $$k_{\min} = \lceil M / N \rceil$$ 
2) Because $b$ might start near the end of one copy of $a$ and wrap across multiple full copies of $a$ before terminating inside a final copy of $a$, $b$ can span at most **$k_{\min} + 1$ (or $k_{\min} + 2$) copies of $a$**! 
3) Repeating $a$ beyond $k_{\min} + 2$ times is mathematically redundant. If $b$ is not a substring within $k_{\min} + 2$ copies, it will **NEVER** be a substring! 
**Algorithm**: 
- Repeat $a$ until $\text{len}(S) \ge M$. Let repetition count be $count$. 
- If $b$ is a substring of $S$ (checked in $\mathcal{O}(|S|)$ via Rabin-Karp / KMP / `find`), return $count$. 
- Else append $a$ one more time: $S += a$, $count++$. If $b$ is a substring of $S$, return $count$. 
- Else return $-1$. Runs in $\mathcal{O}(N + M)$ time and $\mathcal{O}(N + M)$ space.

- **Underlying Pattern**: `String Repetition Bound Invariant + Rabin-Karp Rolling Hash Substring Search`.

---

## 3. Approach 1 — Naive (Unbounded String Search)

### Idea
Append $a$ in a loop without bounding and perform standard quadratic substring searches in $\mathcal{O}((N + M) \times M)$ time.

### C++17 Code
```cpp
#include <string>
using namespace std;

class SolutionNaive {
public:
    int repeatedStringMatch(string a, string b) {
        string s = a;
        int count = 1;
        while (s.size() < b.size()) {
            s += a;
            count++;
        }
        if (s.find(b) != string::npos) return count;
        s += a;
        count++;
        if (s.find(b) != string::npos) return count;
        return -1;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}((N + M) \times M)$ worst-case time using naive substring search.
- **Space Complexity**: $\mathcal{O}(N + M)$ string space.
- **Why it's not good enough**: Naive `find()` can take quadratic time on repetitive inputs like `a = "aaaa..."` and `b = "aaaa...b"`.

---

## 4. Approach 2 — Better (Bounded Repetition + KMP Algorithm)

### Idea
Bounded Repetition + KMP Substring Search in O(N + M) time.

### C++17 Code
```cpp
#include <string>
#include <vector>
using namespace std;

class SolutionKMP {
    bool kmpSearch(const string& text, const string& pat) {
        int n = text.size(), m = pat.size();
        vector<int> lps(m, 0);
        for (int i = 1, len = 0; i < m; ) {
            if (pat[i] == pat[len]) lps[i++] = ++len;
            else if (len) len = lps[len - 1];
            else lps[i++] = 0;
        }
        for (int i = 0, j = 0; i < n; ) {
            if (text[i] == pat[j]) { i++; j++; }
            if (j == m) return true;
            else if (i < n && text[i] != pat[j]) {
                if (j) j = lps[j - 1];
                else i++;
            }
        }
        return false;
    }
public:
    int repeatedStringMatch(string a, string b) {
        string s = "";
        int count = 0;
        while (s.size() < b.size()) { s += a; count++; }
        if (kmpSearch(s, b)) return count;
        s += a; count++;
        if (kmpSearch(s, b)) return count;
        return -1;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N + M)$ time.
- **Space Complexity**: $\mathcal{O}(N + M)$ space.
- **Why it's still not optimal**: KMP requires constructing the explicit LPS auxiliary array.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Rabin-Karp Rolling Hash)

### Idea
Rabin-Karp Polynomial Rolling Hash with Modulo Arithmetic in $\mathcal{O}(N + M)$ time and $\mathcal{O}(N + M)$ space.

### C++17 Code
```cpp
#include <string>
#include <vector>
using namespace std;

class Solution {
private:
    const long long BASE = 31;
    const long long MOD = 1e9 + 7;
    
    // Rabin-Karp Rolling Hash Substring Search in O(text.size() + pat.size())
    bool rabinKarp(const string& text, const string& pat) {
        int n = text.size();
        int m = pat.size();
        if (n < m) return false;
        
        long long patHash = 0;
        long long textHash = 0;
        long long power = 1;
        
        // Compute base^(m - 1) % MOD and initial window hash
        for (int i = 0; i < m; i++) {
            patHash = (patHash * BASE + (pat[i] - 'a' + 1)) % MOD;
            textHash = (textHash * BASE + (text[i] - 'a' + 1)) % MOD;
            if (i < m - 1) {
                power = (power * BASE) % MOD;
            }
        }
        
        // Slide the window across text
        for (int i = 0; i <= n - m; i++) {
            if (patHash == textHash) {
                // Confirm actual substring match to prevent hash collisions
                if (text.substr(i, m) == pat) {
                    return true;
                }
            }
            
            // Roll hash to next position: remove leading char, add trailing char
            if (i < n - m) {
                textHash = (textHash - (text[i] - 'a' + 1) * power) % MOD;
                if (textHash < 0) textHash += MOD;
                textHash = (textHash * BASE + (text[i + m] - 'a' + 1)) % MOD;
            }
        }
        
        return false;
    }
    
public:
    int repeatedStringMatch(string a, string b) {
        string s = "";
        int count = 0;
        
        // Step 1: Repeat string a until its length is at least length of b
        while (s.size() < b.size()) {
            s += a;
            count++;
        }
        
        // Step 2: Check if b is a substring of s
        if (rabinKarp(s, b)) {
            return count;
        }
        
        // Step 3: Append one more copy of a to cover boundary wrap-around
        s += a;
        count++;
        if (rabinKarp(s, b)) {
            return count;
        }
        
        // Impossible for b to be a substring of any repetition of a
        return -1;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N + M)$ average time (rolling hash updates in $\mathcal{O}(1)$ per character).
- **Space Complexity**: $\mathcal{O}(N + M)$ space (constructed string buffer).
- **Why this is optimal**: Rolling hash evaluates each sliding window in $\mathcal{O}(1)$ time with minimal arithmetic operations.

---

## 6. Dry Run

`a = "abcd"`, `b = "cdabcdab"` ($N = 4, M = 8$)

| Step | Action / State Change | Result |
|---|---|---|
| `Initial Repeat` | `s = "abcdabcd"` (len 8), `count = 2` | len(s) >= len(b) |
| `Check count = 2` | `rabinKarp("abcdabcd", "cdabcdab")` $\implies$ false | Not found |
| `Append +1 copy` | `s = "abcdabcdabcd"` (len 12), `count = 3` | One more copy added |
| `Check count = 3` | `rabinKarp("abcdabcdabcd", "cdabcdab")` $\implies$ true at index 2 (`"cdabcdab"`) | Found match! |
| `Result` | Return count = 3 | Minimum Repeats = 3 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $b$ already a substring of $a$ without repetition (returns 1).
- Character in $b$ never exists in $a$ (returns -1 immediately).
- $a = "a", b = "aaaaa"$ (returns $M$).

### Common Bugs to Avoid
- Testing only $k_{\min}$ without the extra $+1$ copy (fails when $b$ wraps across boundary like `a = "abcd"`, `b = "cdabcdab"`).
- Negative hash modulo arithmetic in rolling hash.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is at most ONE extra copy of 'a' beyond length(b) sufficient?**  
  **A**: Because if $b$ starts inside $a$, the start offset is at index $p \in [0, |a|-1]$. To cover all $|b|$ characters, the end index is $p + |b| - 1 < |a| + |b|$. The minimum number of complete $|a|$-blocks to cover length $|a| + |b|$ is $\lceil (|a| + |b|) / |a| \rceil = 1 + \lceil |b| / |a| \rceil$. Testing $k_{\min}$ and $k_{\min} + 1$ covers 100% of all possible valid alignments!

- **Q2: How to protect Rabin-Karp against adversarial anti-hash test cases?**  
  **A**: Use **Double Hashing** with two distinct large prime moduli (e.g. $MOD_1 = 10^9 + 7, MOD_2 = 10^9 + 9$) and two distinct bases (e.g. $B_1 = 31, B_2 = 37$). Collision probability becomes $\approx 10^{-18}$!

- **Q3: Can this be solved in O(1) auxiliary space without string concatenation?**  
  **A**: YES! Virtualize string $a^k$ using modulo index mapping `a[i % a.size()]` and run KMP directly over the virtual stream in $\mathcal{O}(N + M)$ time and $\mathcal{O}(M)$ space!


---

## 9. Tags & Related Problems

- **Tags**: `Strings`, `Rabin-Karp`, `Rolling Hash`, `KMP`, `LeetCode-686`, `Medium`
- **Related problems to practice next**:
- **KMP Algorithm**: String matching.
- **Shortest Palindrome**: KMP prefix application.
