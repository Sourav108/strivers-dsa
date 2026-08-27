# Character Hashing with ASCII Arrays (Step 1.6 — Learn Basic Hashing)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Character Hashing with ASCII Arrays](https://takeuforward.org/data-structure/character-hashing-explained/)
- **Difficulty**: Easy
- **Statement**: Implement character frequency counting for lowercase letters (`a-z`), uppercase letters (`A-Z`), and full 256 ASCII character sets using direct integer indexing arrays.

---

## 1. Problem, Restated

Use character ASCII integer values as direct array indices: `hash[ch - 'a']` in $\mathcal{O}(1)$ time and space.

- **Input**: Parameters specified.
- **Output**: Value or side-effect meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Characters are 1-byte integers under the hood (`'a' = 97`, `'b' = 98`). By subtracting `'a'`, lowercase letters map directly to contiguous indices $0$ to $25$. An array `int hash[26] = {0}` acts as a collision-free perfect hash table with zero hash overhead.

- **Underlying Pattern**: `Direct Address Table (ASCII Array Hashing)`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Using `std::map<char, int>` with Red-Black Tree overhead in $\mathcal{O}(N \log 26)$.

### C++17 Code
```cpp
#include <map>
#include <string>
using namespace std;
map<char, int> charMap(const string& s) {
    map<char, int> m;
    for (char c : s) m[c]++;
    return m;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log 26)$ time.
- **Space Complexity**: $\mathcal{O}(26)$ tree nodes.
- **Why it's not good enough**: Tree balancing overhead.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Direct 26-element stack array: `hash[c - 'a']++` in $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <iostream>
#include <string>
#include <vector>
using namespace std;

class Solution {
public:
    void countCharFrequency(const string& s) {
        // Direct ASCII table for lowercase English letters
        int hash[26] = {0};
        
        for (char ch : s) {
            hash[ch - 'a']++; // O(1) direct memory offset
        }
        
        // Full ASCII table (all 256 characters)
        int asciiHash[256] = {0};
        for (char ch : s) {
            asciiHash[(unsigned char)ch]++;
        }
        
        for (int i = 0; i < 26; i++) {
            if (hash[i] > 0) {
                cout << (char)('a' + i) << ": " << hash[i] << "\n";
            }
        }
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ strictly (26 or 256 integer elements).
- **Why this is optimal**: Direct memory offset indexing with 0 hash function overhead.

---

## 6. Dry Run

Hashing `"abacb"`

| Step | Action / State Change | Result |
|---|---|---|
| `'a'` | 'a' - 'a' = 0 | hash[0] = 1 |
| `'b'` | 'b' - 'a' = 1 | hash[1] = 1 |
| `'a'` | 'a' - 'a' = 0 | hash[0] = 2 |
| `'c'` | 'c' - 'a' = 2 | hash[2] = 1 |
| `'b'` | 'b' - 'a' = 1 | hash[1] = 2 |
| `Result` | a: 2, b: 2, c: 1 | Instant $\mathcal{O}(1)$ lookup ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Strings with non-ASCII or extended characters (cast to `unsigned char`).
- Empty string.

### Common Bugs to Avoid
- Out-of-bounds array access if uppercase character `'A'` is passed to `ch - 'a'` (`65 - 97 = -32` -> segfault!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why cast to (unsigned char) when indexing 256 array?**  
  **A**: On platforms where `char` is signed, characters $\ge 128$ evaluate to negative numbers $(-128 \dots -1)$. Casting to `unsigned char` ensures indices lie safely in $[0, 255]$.


---

## 9. Tags & Related Problems

- **Tags**: `Hashing`, `Strings`, `ASCII`, `Easy`
- **Related problems to practice next**:
- **Valid Anagram**: Anagram checking with 26-array.
