# Implement Trie II (Prefix Tree) (countWordsEqualTo, countWordsStartingWith, erase) (Step 17.1 — Theory & Practice)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Implement Trie II (Prefix Tree) (countWordsEqualTo, countWordsStartingWith, erase)](https://takeuforward.org/data-structure/implement-trie-ii/)
- **Difficulty**: Medium
- **Statement**: Implement a data structure called `Trie` that supports the following operations: (1) `Trie()`: Initializes the Trie object; (2) `void insert(string &word)`: Inserts string `word` into the trie; (3) `int countWordsEqualTo(string &word)`: Returns how many times string `word` is present in the trie; (4) `int countWordsStartingWith(string &prefix)`: Returns how many words in the trie have string `prefix` as a prefix; (5) `void erase(string &word)`: Erases one occurrence of string `word` from the trie. Assume `erase` is only called on words that currently exist in the trie.

---

## 1. Problem, Restated

Design and implement Trie II supporting exact word counting (`countWordsEqualTo`), prefix counting (`countWordsStartingWith`), and deletion (`erase`) in $\mathcal{O}(L)$ time per query using `cntEndsWith` and `cntPrefix` node tracking.

- **Input**: Sequence of `insert`, `countWordsEqualTo`, `countWordsStartingWith`, and `erase` calls.
- **Output**: Integer counts for `countWordsEqualTo` and `countWordsStartingWith`.
- **Complexity Goal**: $\mathcal{O}(L)$ time per query.

---

## 2. Intuition & Pattern

**The Frequency-Augmented Trie Node**: 
Instead of a simple boolean `flag`, augment each node with two integer frequency counters: 
1) `cntPrefix`: Tracks how many inserted words pass through / share this node as a prefix. 
2) `cntEndsWith`: Tracks how many exact words terminate at this node. 
**Operations**: 
- **`insert(word)`**: Start at `root`. For each char $c$: create node if missing, advance `node = node->get(c)`, and increment `node->increasePrefix()` (`cntPrefix++`). After the loop, increment `node->increaseEnd()` (`cntEndsWith++`). 
- **`countWordsEqualTo(word)`**: Traverse `word`. If any char missing $\implies 0$. At the end, return `node->getEnd()` (`cntEndsWith`). 
- **`countWordsStartingWith(prefix)`**: Traverse `prefix`. If any char missing $\implies 0$. At the end, return `node->getPrefix()` (`cntPrefix`). 
- **`erase(word)`**: Traverse `word`. For each char, advance `node = node->get(c)` and decrement `node->reducePrefix()` (`cntPrefix--`). After the loop, decrement `node->deleteEnd()` (`cntEndsWith--`). 
**Complexity**: All operations run in strictly $\mathcal{O}(L)$ time with $\mathcal{O}(1)$ direct pointer dereferencing!

- **Underlying Pattern**: `Trie Node with Frequency Counters (`cntEndsWith` & `cntPrefix`)`.

---

## 3. Approach 1 — Naive (Hash Map + Vector Scanning)

### Idea
Use `std::unordered_map<string, int>` for exact counts and linear prefix scanning for `countWordsStartingWith` in $\mathcal{O}(N \times L)$ time.

### C++17 Code
```cpp
#include <string>
#include <unordered_map>
#include <vector>
using namespace std;

class TrieIINaive {
    unordered_map<string, int> wordCounts;
    vector<string> allWords;
public:
    void insert(string &word) {
        wordCounts[word]++;
        allWords.push_back(word);
    }
    int countWordsEqualTo(string &word) {
        return wordCounts[word];
    }
    int countWordsStartingWith(string &prefix) {
        int cnt = 0;
        for (const string& w : allWords) {
            if (w.rfind(prefix, 0) == 0) cnt++; // O(N * L) linear prefix scanning
        }
        return cnt;
    }
    void erase(string &word) {
        if (wordCounts[word] > 0) wordCounts[word]--;
        for (auto it = allWords.begin(); it != allWords.end(); ++it) {
            if (*it == word) { allWords.erase(it); break; }
        }
    }
};
```

### Complexity Derivation
- **Time Complexity**: `insert`: $\mathcal{O}(L)$, `countWordsEqualTo`: $\mathcal{O}(L)$, `countWordsStartingWith`: $\mathcal{O}(N \times L)$, `erase`: $\mathcal{O}(N \times L)$ time.
- **Space Complexity**: $\mathcal{O}(N \times L)$ space.
- **Why it's not good enough**: Prefix counting and erase operations scan through all $N$ words sequentially.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard frequency-augmented Trie below directly achieves optimal $\mathcal{O}(L)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Frequency-Augmented Trie)

### Idea
Frequency-Augmented Trie Node (`cntPrefix` and `cntEndsWith`) in $\mathcal{O}(L)$ time per operation.

### C++17 Code
```cpp
#include <string>
#include <vector>
using namespace std;

// Frequency-Augmented Trie Node
struct Node {
    Node* links[26];
    int cntEndsWith = 0; // Number of words ending at this node
    int cntPrefix = 0;   // Number of words sharing this prefix
    
    bool containsKey(char ch) {
        return links[ch - 'a'] != nullptr;
    }
    
    Node* get(char ch) {
        return links[ch - 'a'];
    }
    
    void put(char ch, Node* node) {
        links[ch - 'a'] = node;
    }
    
    void increaseEnd() {
        cntEndsWith++;
    }
    
    void increasePrefix() {
        cntPrefix++;
    }
    
    void deleteEnd() {
        cntEndsWith--;
    }
    
    void reducePrefix() {
        cntPrefix--;
    }
    
    int getEnd() {
        return cntEndsWith;
    }
    
    int getPrefix() {
        return cntPrefix;
    }
};

class Trie {
private:
    Node* root;
    
public:
    Trie() {
        root = new Node();
    }
    
    // Inserts a word, updating both prefix and end counters in O(L)
    void insert(string &word) {
        Node* node = root;
        for (char ch : word) {
            if (!node->containsKey(ch)) {
                node->put(ch, new Node());
            }
            node = node->get(ch);
            node->increasePrefix();
        }
        node->increaseEnd();
    }
    
    // Returns exact frequency of word in O(L)
    int countWordsEqualTo(string &word) {
        Node* node = root;
        for (char ch : word) {
            if (!node->containsKey(ch)) {
                return 0;
            }
            node = node->get(ch);
        }
        return node->getEnd();
    }
    
    // Returns count of words starting with prefix in O(L)
    int countWordsStartingWith(string &prefix) {
        Node* node = root;
        for (char ch : prefix) {
            if (!node->containsKey(ch)) {
                return 0;
            }
            node = node->get(ch);
        }
        return node->getPrefix();
    }
    
    // Erases one occurrence of word in O(L)
    void erase(string &word) {
        Node* node = root;
        for (char ch : word) {
            if (!node->containsKey(ch)) {
                return; // Word does not exist
            }
            node = node->get(ch);
            node->reducePrefix();
        }
        node->deleteEnd();
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(L)$ time for `insert`, `countWordsEqualTo`, `countWordsStartingWith`, and `erase` (where $L$ is word length).
- **Space Complexity**: $\mathcal{O}(N \times L \times 26)$ space (compressed significantly through prefix node sharing).
- **Why this is optimal**: Direct counter maintenance on Trie nodes eliminates search loops and provides immediate $\mathcal{O}(1)$ counter lookups.

---

## 6. Dry Run

`insert("samsung")`, `insert("samsung")`, `insert("vivo")`, `erase("samsung")`, `countWordsEqualTo("samsung")`, `countWordsStartingWith("sams")`

| Step | Action / State Change | Result |
|---|---|---|
| `insert("samsung")` | root->'s'->'a'->'m'->'s'->'u'->'n'->'g'. All prefix counts = 1, 'g' end count = 1 | Trie has 1 "samsung" |
| `insert("samsung")` | Traverses same nodes. Prefix counts increment to 2, 'g' end count = 2 | Trie has 2 "samsung" |
| `insert("vivo")` | root->'v'->'i'->'v'->'o'. Prefix counts = 1, 'o' end count = 1 | Trie has 1 "vivo" |
| `erase("samsung")` | Traverses 's'..'g'. Prefix counts decrement from 2 to 1, 'g' end count becomes 1 | 1 "samsung" erased |
| `countWordsEqualTo("samsung")` | Traverses to 'g', returns node->getEnd() = 1 | Returns 1 ✅ |
| `countWordsStartingWith("sams")` | Traverses to second 's', returns node->getPrefix() = 1 | Returns 1 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Erasing a word with duplicate occurrences (only 1 occurrence should be decremented).
- Calling `countWordsStartingWith` on non-existent prefix (returns 0 safely).
- Words sharing common prefixes (`"apple"` and `"app"`).

### Common Bugs to Avoid
- Incrementing `cntPrefix` at the parent node before moving to `node->get(ch)` (prefix count belongs to the CHILD representing character `ch`).
- Deleting heap nodes physically on erase without checking if other words still branch through them (counters handle virtual deletion safely without pointer corruption!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is virtual deletion (decrementing cntPrefix and cntEndsWith) preferred over physical memory deletion?**  
  **A**: Because physically freeing a `Node*` when deleting word `"app"` would corrupt pointers for word `"apple"` that branches through the same `p` node! By simply decrementing `cntPrefix` and `cntEndsWith`, if `cntPrefix == 0`, the subtree is logically unreachable with zero pointer dangling risks!

- **Q2: How to physically reclaim memory of dead subtrees when cntPrefix reaches 0?**  
  **A**: When `node->cntPrefix == 0`, we can prune the branch: set `parent->links[ch - 'a'] = nullptr` and call recursive destructor `deleteSubtree(node)` to reclaim heap memory!

- **Q3: How does Trie II support Wildcard Search (e.g. 's.m.ung' where '.' matches any char)?**  
  **A**: Use DFS backtracking on Trie: when char is a letter, advance to `links[ch - 'a']`; when char is `'.'`. iterate through all non-null children `links[0 ... 25]`!


---

## 9. Tags & Related Problems

- **Tags**: `Trie`, `Prefix Tree`, `Design`, `Frequency Counters`, `Medium`
- **Related problems to practice next**:
- **Implement Trie I**: Boolean flag predecessor.
- **Longest Word with All Prefixes**: Trie completeness check.
