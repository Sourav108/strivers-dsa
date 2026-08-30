# Longest Word with All Prefixes (Complete String) (Step 17.1 — Theory & Practice)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Longest Word with All Prefixes (Complete String)](https://takeuforward.org/data-structure/longest-word-with-all-prefixes/)
- **Difficulty**: Medium
- **Statement**: Given an array of strings `a` of size $N$, find the longest string in the array such that all of its prefixes are also present in the array. If there are multiple strings with the same maximum length, return the lexicographically smallest one. If no such string exists, return `"None"`.

---

## 1. Problem, Restated

Find the longest (and lexicographically smallest) word where every non-empty prefix is marked as a complete word (`flag == true`) in a Trie in $\mathcal{O}(N \times L)$ time and $\mathcal{O}(N \times L \times 26)$ space.

- **Input**: Array of $N$ strings.
- **Output**: Longest complete string or `"None"`.
- **Complexity Goal**: $\mathcal{O}(N \times L)$ time and space.

---

## 2. Intuition & Pattern

**The Complete String Invariant**: 
A word $W = c_1 c_2 \dots c_L$ is valid if and only if EVERY prefix $c_1$, $c_1 c_2$, ..., $c_1 c_2 \dots c_L$ was explicitly inserted into the dataset. 
**Trie Verification**: 
1) Insert ALL $N$ words into a standard Trie. 
2) For each candidate word $W$: 
   - Traverse the Trie from `root` following characters of $W$. 
   - At EVERY step, check: `if (!node->isEnd()) return false;` (if ANY intermediate prefix node has `flag == false`, this word is disqualified!). 
   - If all $L$ nodes have `flag == true`, $W$ is a valid complete string! 
3) Maintain `longestWord`: 
   - If $\text{len}(W) > \text{len}(\text{longestWord})$, update `longestWord = W`. 
   - If $\text{len}(W) == \text{len}(\text{longestWord})$ and $W < \text{longestWord}$ (lexicographical comparison), update `longestWord = W`. 
4) If no word satisfies the condition, return `"None"`. Runs in $\mathcal{O}(N \times L)$ time and $\mathcal{O}(N \times L \times 26)$ space.

- **Underlying Pattern**: `Trie Prefix Completeness Traversal / All-Prefix Flag Verification`.

---

## 3. Approach 1 — Naive (Hash Set Substring Generation)

### Idea
Use `std::unordered_set<string>` and check all $L$ prefixes of every word in $\mathcal{O}(N \times L^2)$ time.

### C++17 Code
```cpp
#include <string>
#include <vector>
#include <unordered_set>
#include <algorithm>
using namespace std;

class SolutionNaive {
public:
    string completeString(int n, vector<string> &a) {
        unordered_set<string> setA(a.begin(), a.end());
        string longest = "";
        for (const string& word : a) {
            bool valid = true;
            for (int len = 1; len <= word.size(); len++) {
                if (setA.find(word.substr(0, len)) == setA.end()) {
                    valid = false;
                    break;
                }
            }
            if (valid) {
                if (word.size() > longest.size() || (word.size() == longest.size() && word < longest)) {
                    longest = word;
                }
            }
        }
        return longest.empty() ? "None" : longest;
    }
};
```

### Java Code
```java
import java.util.*;

class SolutionNaive {

    String completeString(int n, String[] a) {
        Set<String> setA(a.begin(), a.end());
        String longest = "";
        for (String word : a) {
            boolean valid = true;
            for (int len = 1; len <= word.length; len++) {
                if (setA.find(word.substring(0, 0 + len)) == setA.end()) {
                    valid = false;
                    break;
                }
            }
            if (valid) {
                if (word.length > longest.length || (word.length == longest.length && word < longest)) {
                    longest = word;
                }
            }
        }
        return longest.isEmpty() ? "None" : longest;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times L^2)$ time (generating substrings and hashing takes $\mathcal{O}(L^2)$ per word).
- **Space Complexity**: $\mathcal{O}(N \times L)$ hash set space.
- **Why it's not good enough**: Substring generation creates unnecessary memory copies and hashing overhead.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Trie validation below directly achieves optimal $\mathcal{O}(N \times L)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Trie Prefix Verification)

### Idea
Trie Insertion + Single Prefix-Completeness Verification Pass in $\mathcal{O}(N \times L)$ time and $\mathcal{O}(N \times L \times 26)$ space.

### C++17 Code
```cpp
#include <vector>
#include <string>
#include <algorithm>
using namespace std;

// Standard 26-Way Trie Node
struct Node {
    Node* links[26];
    bool flag = false;
    
    bool containsKey(char ch) {
        return links[ch - 'a'] != nullptr;
    }
    
    Node* get(char ch) {
        return links[ch - 'a'];
    }
    
    void put(char ch, Node* node) {
        links[ch - 'a'] = node;
    }
    
    void setEnd() {
        flag = true;
    }
    
    bool isEnd() {
        return flag;
    }
};

class Trie {
private:
    Node* root;
    
public:
    Trie() {
        root = new Node();
    }
    
    // Inserts a word into the trie
    void insert(const string& word) {
        Node* node = root;
        for (char ch : word) {
            if (!node->containsKey(ch)) {
                node->put(ch, new Node());
            }
            node = node->get(ch);
        }
        node->setEnd();
    }
    
    // Checks if ALL prefixes of word exist as complete words in Trie
    bool checkIfAllPrefixesExist(const string& word) {
        Node* node = root;
        for (char ch : word) {
            if (!node->containsKey(ch)) {
                return false;
            }
            node = node->get(ch);
            // If any intermediate prefix is NOT a complete word, invalid!
            if (!node->isEnd()) {
                return false;
            }
        }
        return true;
    }
};

class Solution {
public:
    string completeString(int n, vector<string> &a) {
        Trie trie;
        
        // Step 1: Insert all words into the Trie in O(N * L)
        for (const string& word : a) {
            trie.insert(word);
        }
        
        string longest = "";
        
        // Step 2: Validate each word in O(L) time
        for (const string& word : a) {
            if (trie.checkIfAllPrefixesExist(word)) {
                if (word.size() > longest.size()) {
                    longest = word;
                } else if (word.size() == longest.size() && word < longest) {
                    longest = word; // Tie-breaker: Lexicographically smaller string
                }
            }
        }
        
        return longest.empty() ? "None" : longest;
    }
};
```

### Java Code
```java
// Standard 26-Way Trie Node
static class Node {
    Node  links[26];
    boolean flag = false;
    
    boolean containsKey(char ch) {
        return links[ch - 'a'] != null;
    }
    
    Node  get(char ch) {
        return links[ch - 'a'];
    }
    
    void put(char ch, Node  node) {
        links[ch - 'a'] = node;
    }
    
    void setEnd() {
        flag = true;
    }
    
    boolean isEnd() {
        return flag;
    }
};

class Trie {

    Node  root;

    Trie() {
        root = new Node();
    }
    
    // Inserts a word into the trie
    void insert(String word) {
        Node  node = root;
        for (char ch : word) {
            if (!node.containsKey(ch)) {
                node.put(ch, new Node());
            }
            node = node.get(ch);
        }
        node.setEnd();
    }
    
    // Checks if ALL prefixes of word exist as complete words in Trie
    boolean checkIfAllPrefixesExist(String word) {
        Node  node = root;
        for (char ch : word) {
            if (!node.containsKey(ch)) {
                return false;
            }
            node = node.get(ch);
            // If any intermediate prefix is NOT a complete word, invalid!
            if (!node.isEnd()) {
                return false;
            }
        }
        return true;
    }
};

class Solution {

    String completeString(int n, String[] a) {
        Trie trie;
        
        // Step 1: Insert all words into the Trie in O(N * L)
        for (String word : a) {
            trie.add(word);
        }
        
        String longest = "";
        
        // Step 2: Validate each word in O(L) time
        for (String word : a) {
            if (trie.checkIfAllPrefixesExist(word)) {
                if (word.length > longest.length) {
                    longest = word;
                } else if (word.length == longest.length && word < longest) {
                    longest = word; // Tie-breaker: Lexicographically smaller String
                }
            }
        }
        
        return longest.isEmpty() ? "None" : longest;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times L)$ time (Insertions: $\mathcal{O}(N \times L)$; Validation: $\mathcal{O}(N \times L)$ where $L$ is max word length).
- **Space Complexity**: $\mathcal{O}(N \times L \times 26)$ space (Trie structure with prefix sharing).
- **Why this is optimal**: Trie validates all $L$ prefixes simultaneously along the path in a single $\mathcal{O}(L)$ pass without creating string copies.

---

## 6. Dry Run

`a = ["n", "ni", "nin", "ninj", "ninja", "ninga"]` ($N = 6$)

| Step | Action / State Change | Result |
|---|---|---|
| `Trie Build` | Inserts all words. All nodes 'n', 'i', 'n', 'j', 'a' have flag = true. 'g' in "ninga" has flag = true | All nodes inserted |
| `Check "ninja"` | 'n'(T) -> 'i'(T) -> 'n'(T) -> 'j'(T) -> 'a'(T) $\implies$ Valid! length = 5 | longest = "ninja" |
| `Check "ninga"` | 'n'(T) -> 'i'(T) -> 'n'(T) -> 'g'(T) -> 'a'(T) $\implies$ Valid! length = 5 | Tie detected |
| `Tie-breaker` | "ninja" vs "ninga": "ninga" < "ninja" lexicographically | longest = "ninga" |
| `Result` | Return "ninga" | Output = "ninga" ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- No word has all prefixes present (returns `"None"`).
- Single character word `["a"]` (returns `"a"`).
- Words with gap prefixes (e.g. `"a"`, `"abc"` but missing `"ab"`).

### Common Bugs to Avoid
- Missing tie-breaker: taking first longest word instead of lexicographically smaller one.
- Returning empty string `""` instead of `"None"` when no valid word exists.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Can we find the longest complete string using a DFS traversal on the Trie?**  
  **A**: YES! Start a DFS from `root`. Only branch to children that have `child->isEnd() == true`. Traverse children in alphabetical order from `'a'` to `'z'`. The deepest DFS path directly yields the longest, lexicographically smallest complete word!

- **Q2: How does DFS compare to iterating the array with checkIfAllPrefixesExist?**  
  **A**: Both have $\mathcal{O}(N \times L)$ time. The DFS approach visits only valid nodes and avoids re-validating common prefixes, making it slightly faster in practice.

- **Q3: What if the alphabet contains uppercase, lowercase, and digits?**  
  **A**: Replace fixed 26-pointer array with an array of size 62 (or 128 ASCII), or use `std::unordered_map<char, Node*>` for universal character set support!


---

## 9. Tags & Related Problems

- **Tags**: `Trie`, `Prefix Tree`, `Strings`, `DFS`, `Medium`
- **Related problems to practice next**:
- **Implement Trie I**: Base Trie operations.
- **Word Break**: Prefix dictionary matching.
