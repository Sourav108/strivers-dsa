# Reverse Words in a Given String (Step 5.1 — Basic and Easy String Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Reverse Words in a Given String](https://takeuforward.org/data-structure/reverse-words-in-a-string/)
- **Difficulty**: Medium
- **Statement**: Given an input string $s$, reverse the order of the words. A word is defined as a sequence of non-space characters. Words will be separated by at least one space. Return a string with words in reverse order joined by a single space, removing leading/trailing and multiple spaces.

---

## 1. Problem, Restated

Reverse words in a sentence while cleaning whitespace in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ auxiliary space.

- **Input**: Problem parameters.
- **Output**: Transformed string or calculated integer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) Clean leading, trailing, and multiple spaces. 2) Reverse the entire string. 3) Reverse each individual word. Example: `"the sky is blue"` $\implies$ `"eulb si yks eht"` $\implies$ `"blue is sky the"`.

- **Underlying Pattern**: `Two-Pass In-Place Reversal (Reverse Whole String, then Reverse Each Word)`.

---

## 3. Approach 1 — Brute Force / Naive

### Idea
Stringstream tokenization with vector stack in $\mathcal{O}(N)$ extra space.

### C++17 Code
```cpp
#include <string>
#include <sstream>
#include <vector>
using namespace std;
string reverseWordsStream(string s) {
    stringstream ss(s);
    string word, ans = "";
    vector<string> words;
    while (ss >> word) words.push_back(word);
    for (int i = (int)words.size() - 1; i >= 0; i--) {
        ans += words[i];
        if (i > 0) ans += " ";
    }
    return ans;
}
```

### Java Code
```java
import java.util.*;

class Solution {
    String reverseWordsStream(String s) {
        stringstream ss(s);
        String word, ans = "";
        List<String> words = new ArrayList<>();
        while (ss >> word) words.add(word);
        for (int i = words.length - 1; i >= 0; i--) {
            ans += words[i];
            if (i > 0) ans += " ";
        }
        return ans;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ vector storage.
- **Why it's not good enough**: Extra memory for word vector.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
In-place whitespace compression + double reversal algorithm.

### C++17 Code
```cpp
#include <string>
#include <algorithm>
using namespace std;

class Solution {
public:
    string reverseWords(string s) {
        int n = s.length();
        int i = 0, r = 0;
        
        // Step 1: In-place clean multiple spaces
        while (i < n) {
            while (i < n && s[i] == ' ') i++; // skip leading/multiple spaces
            if (i < n) {
                if (r > 0) s[r++] = ' ';       // insert single space separator
                int start = r;
                while (i < n && s[i] != ' ') s[r++] = s[i++];
                reverse(s.begin() + start, s.begin() + r); // reverse individual word
            }
        }
        s.resize(r); // trim trailing space
        
        // Step 2: Reverse the entire sanitized string
        reverse(s.begin(), s.end());
        return s;
    }
};
```

### Java Code
```java
class Solution {

    String reverseWords(String s) {
        int n = s.length();
        int i = 0, r = 0;
        
        // Step 1: In-place clean multiple spaces
        while (i < n) {
            while (i < n && s[i] == ' ') i++; // skip leading/multiple spaces
            if (i < n) {
                if (r > 0) s[r++] = ' ';       // insert single space separator
                int start = r;
                while (i < n && s[i] != ' ') s[r++] = s[i++];
                reverse(s.begin() + start, s.begin() + r); // reverse individual word
            }
        }
        s.resize(r); // trim trailing space
        
        // Step 2: Reverse the entire sanitized String
        reverse(s.begin(), s.end());
        return s;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ linear time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space (in-place mutation).
- **Why this is optimal**: Modifies input string in-place without dynamic vector allocation.

---

## 6. Dry Run

$s = \text{"  hello world  "}$

| Step | Action / State Change | Result |
|---|---|---|
| `Sanitize & Word Rev` | Word 1 'hello' -> 'olleh', Word 2 'world' -> 'dlrow' | `"olleh dlrow"` |
| `Reverse Entire` | reverse("olleh dlrow") | `"world hello"` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Multiple consecutive spaces `"a   b"`.
- Leading and trailing spaces.
- Single word `"word"`.

### Common Bugs to Avoid
- Leaving trailing space at the end of the string.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does double reversal reverse word order?**  
  **A**: Reversing whole string moves the last word to the beginning in reversed letters. Reversing each word flips the letters back into correct spelling while preserving the new word order!


---

## 9. Tags & Related Problems

- **Tags**: `Strings`, `Two Pointers`, `In-Place`, `LeetCode-151`, `Medium`
- **Related problems to practice next**:
- **Valid Anagram**: Character counts.
