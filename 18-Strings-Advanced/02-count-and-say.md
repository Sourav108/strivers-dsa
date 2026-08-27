# Count and Say (Run-Length Sequence) (Step 18.1 — String Matching & Hard Algorithms)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Count and Say (Run-Length Sequence)](https://takeuforward.org/strings/count-and-say/)
- **Difficulty**: Medium
- **Statement**: The count-and-say sequence is a sequence of digit strings defined by the recursive formula: (1) `countAndSay(1) = "1"`, (2) `countAndSay(n)` is the run-length encoding of `countAndSay(n - 1)`. Run-length encoding (RLE) is a string compression method that works by replacing consecutive identical characters (repeated 2 or more times) with the concatenation of the character and the number marking the count of the characters (length of the run). Given a positive integer $n$, return the $n$-th term of the count-and-say sequence.

---

## 1. Problem, Restated

Generate the $n$-th term of the Look-and-Say run-length encoding sequence iteratively by compressing identical adjacent character blocks in $\mathcal{O}(n \times L)$ time and $\mathcal{O}(L)$ space (where $L$ is the length of string $n$).

- **Input**: Integer $n$.
- **Output**: $n$-th count-and-say string.
- **Complexity Goal**: $\mathcal{O}(n \times L)$ time and $\mathcal{O}(L)$ space.

---

## 2. Intuition & Pattern

**The Look-and-Say Run-Length Invariant**: 
Starting with $S_1 = \text{"1"}$: 
To generate $S_k$ from $S_{k-1}$: 
1) Group consecutive identical characters together into contiguous runs: $c_1 c_1 \dots c_1$ (count $C$, character $c$). 
2) Replace the block with `to_string(C) + c`. 
**Two-Pointer Linear Scan**: 
- Scan $S_{k-1}$ with pointer $j$: 
  - Count consecutive matches: `while (j + 1 < len && s[j] == s[j + 1]) { count++; j++; }` 
  - Append `to_string(count) + s[j]` to `nextSeq`. 
- Repeat $n - 1$ times. 
**Conway's Constant ($\lambda \approx 1.303577$)**: The string length grows exponentially by a factor of approximately $1.3035$ per step. For $n = 30$, length is only $\approx 4462$ characters, running in under 2 ms!

- **Underlying Pattern**: `Run-Length Encoding (RLE) Two-Pointer Block Compression`.

---

## 3. Approach 1 — Naive (Direct Recursion)

### Idea
Direct recursion with string concatenation and deep call stack in $\mathcal{O}(n \times L)$ time.

### C++17 Code
```cpp
#include <string>
using namespace std;

class SolutionRecursive {
public:
    string countAndSay(int n) {
        if (n == 1) return "1";
        string prev = countAndSay(n - 1);
        string result = "";
        int len = prev.size();
        for (int i = 0; i < len; i++) {
            int count = 1;
            while (i + 1 < len && prev[i] == prev[i + 1]) {
                count++;
                i++;
            }
            result += to_string(count) + prev[i];
        }
        return result;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n \times L)$ time (where $L$ is length of the string at step $n$).
- **Space Complexity**: $\mathcal{O}(n)$ recursion call stack + string memory.
- **Why it's not good enough**: Recursion overhead is unnecessary when iterative rolling updates work cleanly.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard iterative block scanning below directly achieves optimal bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Iterative Run-Length Block Encoding)

### Idea
Iterative Two-Pointer Run-Length String Transformation in $\mathcal{O}(n \times L)$ time and $\mathcal{O}(L)$ space.

### C++17 Code
```cpp
#include <string>
#include <vector>
using namespace std;

class Solution {
public:
    string countAndSay(int n) {
        if (n == 1) return "1";
        
        string current = "1";
        
        // Generate sequence iteratively from step 2 to n
        for (int step = 2; step <= n; step++) {
            string nextSeq = "";
            int len = current.size();
            
            for (int i = 0; i < len; i++) {
                int count = 1;
                
                // Count consecutive identical characters
                while (i + 1 < len && current[i] == current[i + 1]) {
                    count++;
                    i++;
                }
                
                // Append "count" followed by "character"
                nextSeq += to_string(count);
                nextSeq.push_back(current[i]);
            }
            
            current = nextSeq;
        }
        
        return current;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n \times L)$ time (where $L \approx \lambda^n$ is length of the $n$-th string, $\lambda \approx 1.303577$).
- **Space Complexity**: $\mathcal{O}(L)$ auxiliary space (storing the current and next string buffer).
- **Why this is optimal**: Iterative block scan processes each character in exactly $\mathcal{O}(1)$ without recursion frames.

---

## 6. Dry Run

`n = 5`

| Step | Action / State Change | Result |
|---|---|---|
| `Step 1` | "1" | Base: one '1' $\implies$ "1" |
| `Step 2` | 1 copy of '1' $\implies$ "11" | One 1 |
| `Step 3` | 2 copies of '1' $\implies$ "21" | Two 1s |
| `Step 4` | 1 copy of '2', 1 copy of '1' $\implies$ "1211" | One 2, One 1 |
| `Step 5` | 1 copy of '1', 1 copy of '2', 2 copies of '1' $\implies$ "111221" | One 1, One 2, Two 1s |
| `Result` | Return "111221" | Output = "111221" ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $n = 1$ (returns `"1"`).
- $n = 2$ (returns `"11"`).
- $n = 30$ (maximum LeetCode constraint, length $\approx 4462$).

### Common Bugs to Avoid
- Out of bounds on `i + 1 < len` condition.
- Appending character before count (`"1" + count` instead of `count + "1"`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is Conway's Constant and Cosmological Theorem in the Look-and-Say sequence?**  
  **A**: Mathematician John Conway proved that the ratio of lengths of consecutive terms $\lim_{n \to \infty} \frac{L_{n+1}}{L_n} = \lambda \approx 1.303577269...$, where $\lambda$ is the unique positive real root of a 71-degree algebraic polynomial! Furthermore, any sequence eventually decays into 92 common 'elements' (called Conway's Cosmological Theorem)!

- **Q2: Can any digit other than '1', '2', or '3' ever appear in the sequence starting from '1'?**  
  **A**: NO! Starting with `'1'`, the digits `'4'` or higher will NEVER appear in the sequence for ANY $n \in \mathbb{N}$! No run of 4 identical consecutive characters is mathematically possible!

- **Q3: How to optimize string building performance in C++ for large n?**  
  **A**: Use `std::string::reserve()` with estimated capacity $\lceil L \times 1.31 \rceil$ to eliminate repeated heap reallocations!


---

## 9. Tags & Related Problems

- **Tags**: `Strings`, `Run-Length Encoding`, `Two Pointers`, `Simulation`, `LeetCode-38`, `Medium`
- **Related problems to practice next**:
- **String Compression**: In-place RLE.
- **Encode and Decode Strings**: String serialization.
