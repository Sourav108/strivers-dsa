# Implement Trie (Prefix Tree) I (Insert, Search, StartsWith) (Step 17.1 — Theory & Practice)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Implement Trie (Prefix Tree) I (Insert, Search, StartsWith)](https://takeuforward.org/data-structure/implement-trie-1/)
- **Difficulty**: Medium
- **Statement**: A **trie** (pronounced as "try") or **prefix tree** is a tree data structure used to efficiently store and retrieve keys in a dataset of strings. There are various applications of this data structure, such as autocomplete and spellchecker. Implement the `Trie` class: (1) `Trie()` Initializes the trie object; (2) `void insert(String word)` Inserts the string `word` into the trie; (3) `boolean search(String word)` Returns `true` if the string `word` is in the trie (i.e., was inserted before), and `false` otherwise; (4) `boolean startsWith(String prefix)` Returns `true` if there is a previously inserted string `word` that has the prefix `prefix`, and `false` otherwise.

---

## 1. Problem, Restated

Design and implement a Trie (Prefix Tree) supporting `insert`, `search`, and `startsWith` in $\mathcal{O}(L)$ time per query (where $L$ is word length) using a 26-pointer array `Node` structure.

- **Input**: Stream of `insert`, `search`, and `startsWith` function calls.
- **Output**: Boolean results for `search` and `startsWith`.
- **Complexity Goal**: $\mathcal{O}(L)$ time per operation.

---

## 2. Intuition & Pattern

**The Trie Node Architecture**: 
Each Trie Node represents a character transition and contains: 
1) `Node* links[26]`: An array of 26 pointers pointing to child nodes for characters `'a'` through `'z'`. 
2) `bool flag` (or `isEnd`): Indicates whether a complete inserted word terminates at this node. 
**Operations**: 
- **`insert(word)`**: Start at `root`. For each char $c$: if `!node->containsKey(c)`, create a new `Node()`. Move to `node->get(c)`. After the last character, set `node->setEnd()`. 
- **`search(word)`**: Start at `root`. For each char $c$: if `!node->containsKey(c)`, return `false`. Move to `node->get(c)`. After traversing all characters, return `node->isEnd()`. 
- **`startsWith(prefix)`**: Start at `root`. For each char $c$: if `!node->containsKey(c)`, return `false`. Move to `node->get(c)`. If all prefix characters exist, return `true` (does NOT require `isEnd`). 
**Complexity**: All operations run in $\mathcal{O}(L)$ time where $L$ is the length of the query string, independent of the total number of words in the dataset!

- **Underlying Pattern**: `26-Way Trie Node with Boolean End-of-Word Flag (`isEnd`)`.

---

## 3. Approach 1 — Naive (Hash Set + Vector Scanning)

### Idea
Use `std::unordered_set<string>` for exact search and iterate over all inserted words for `startsWith` in $\mathcal{O}(N \times L)$ time.

### C++17 Code
```cpp
#include <string>
#include <unordered_set>
#include <vector>
using namespace std;

class TrieNaive {
    unordered_set<string> dict;
    vector<string> words;
public:
    void insert(string word) {
        dict.insert(word);
        words.push_back(word);
    }
    bool search(string word) {
        return dict.find(word) != dict.end();
    }
    bool startsWith(string prefix) {
        for (const string& w : words) {
            if (w.rfind(prefix, 0) == 0) return true; // O(N * L) prefix scanning
        }
        return false;
    }
};
```

### Complexity Derivation
- **Time Complexity**: `insert`: $\mathcal{O}(L)$, `search`: $\mathcal{O}(L)$ average, `startsWith`: $\mathcal{O}(N \times L)$ worst-case time.
- **Space Complexity**: $\mathcal{O}(N \times L)$ space.
- **Why it's not good enough**: `startsWith` scans through all $N$ words sequentially.

---

## 4. Approach 2 — Better (Trie with Hash Map Links)

### Idea
Trie with `std::unordered_map<char, Node*>` child links (supports general unicode / arbitrary alphabet sets).

### C++17 Code
```cpp
#include <unordered_map>
#include <string>
using namespace std;

struct MapNode {
    unordered_map<char, MapNode*> children;
    bool isEnd = false;
};

class TrieMap {
    MapNode* root;
public:
    TrieMap() { root = new MapNode(); }
    void insert(string word) {
        MapNode* curr = root;
        for (char c : word) {
            if (!curr->children.count(c)) curr->children[c] = new MapNode();
            curr = curr->children[c];
        }
        curr->isEnd = true;
    }
    bool search(string word) {
        MapNode* curr = root;
        for (char c : word) {
            if (!curr->children.count(c)) return false;
            curr = curr->children[c];
        }
        return curr->isEnd;
    }
    bool startsWith(string prefix) {
        MapNode* curr = root;
        for (char c : prefix) {
            if (!curr->children.count(c)) return false;
            curr = curr->children[c];
        }
        return true;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(L)$ time per query with hash table lookup overhead.
- **Space Complexity**: $\mathcal{O}(\text{total characters})$ dynamic node space.
- **Why it's still not optimal**: Hash map operations involve dynamic memory allocations and bucket hashing overhead.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (26-Way Fixed Pointer Trie)

### Idea
Fixed-Size 26-Pointer Array `Node* links[26]` in $\mathcal{O}(L)$ time and optimal cache locality.

### C++17 Code
```cpp
#include <string>
#include <vector>
using namespace std;

// Trie Node Definition
struct Node {
    Node* links[26];
    bool flag = false;
    
    // Check if character 'ch' is present as a child
    bool containsKey(char ch) {
        return links[ch - 'a'] != nullptr;
    }
    
    // Put character 'ch' link
    void put(char ch, Node* node) {
        links[ch - 'a'] = node;
    }
    
    // Get child node for character 'ch'
    Node* get(char ch) {
        return links[ch - 'a'];
    }
    
    // Mark the end of a word
    void setEnd() {
        flag = true;
    }
    
    // Check if node is the end of a word
    bool isEnd() {
        return flag;
    }
};

class Trie {
private:
    Node* root;
    
public:
    // Initialize Trie
    Trie() {
        root = new Node();
    }
    
    // Inserts a word into the trie in O(L) time
    void insert(string word) {
        Node* node = root;
        for (char ch : word) {
            if (!node->containsKey(ch)) {
                node->put(ch, new Node());
            }
            node = node->get(ch);
        }
        node->setEnd();
    }
    
    // Returns true if word is in the trie in O(L) time
    bool search(string word) {
        Node* node = root;
        for (char ch : word) {
            if (!node->containsKey(ch)) {
                return false;
            }
            node = node->get(ch);
        }
        return node->isEnd();
    }
    
    // Returns true if there is any word in trie starting with prefix in O(L) time
    bool startsWith(string prefix) {
        Node* node = root;
        for (char ch : prefix) {
            if (!node->containsKey(ch)) {
                return false;
            }
            node = node->get(ch);
        }
        return true; // Reaching here means entire prefix was matched
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(L)$ time for `insert`, `search`, and `startsWith` (where $L$ is word length).
- **Space Complexity**: $\mathcal{O}(N \times L \times 26)$ worst-case space (where $N$ is number of words, $L$ average word length). Shared prefixes compress space significantly.
- **Why this is optimal**: 26-way direct array indexing `links[ch - 'a']` provides instant $\mathcal{O}(1)$ pointer dereferencing with zero hash collisions.

---

## 6. Dry Run

`insert("apple")`, `search("apple")`, `search("app")`, `startsWith("app")`, `insert("app")`, `search("app")`

| Step | Action / State Change | Result |
|---|---|---|
| `insert("apple")` | root -> 'a' -> 'p' -> 'p' -> 'l' -> 'e' (flag = true) | Trie has "apple" |
| `search("apple")` | Follows 'a'->'p'->'p'->'l'->'e'. node->isEnd() == true | Returns true ✅ |
| `search("app")` | Follows 'a'->'p'->'p'. Node 'p' has flag == false | Returns false ❌ |
| `startsWith("app")` | Follows 'a'->'p'->'p'. All 3 characters exist | Returns true ✅ |
| `insert("app")` | Reuses existing 'a'->'p'->'p' nodes, sets flag = true at second 'p' | Updated flag |
| `search("app")` | Follows 'a'->'p'->'p'. node->isEnd() is now true | Returns true ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Empty string insertion.
- Prefix query on non-existent character (fails at root).
- Querying words that are prefixes of longer inserted words (`"app"` vs `"apple"`).

### Common Bugs to Avoid
- Returning `true` in `search` without checking `node->isEnd()` (would falsely match prefixes as complete words!).
- Memory leak from un-freed heap nodes (can implement destructor with recursive post-order deletion).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How does Trie search complexity compare to Hash Table (std::unordered_set)?**  
  **A**: Both take $\mathcal{O}(L)$ time to process a string of length $L$ (hashing an $L$-character string takes $\mathcal{O}(L)$ time). However, Trie additionally supports `startsWith` prefix searches in $\mathcal{O}(L)$ and lexicographical ordering, whereas Hash Tables cannot search prefixes without scanning all $N$ entries!

- **Q2: How to implement a destructor for Trie in C++ to prevent memory leaks?**  
  **A**: Write a recursive helper `void deleteNode(Node* node) { if(!node) return; for(int i=0; i<26; i++) deleteNode(node->links[i]); delete node; }` in the `~Trie()` destructor.

- **Q3: What is a Radix Tree (Compressed Trie / Patricia Trie)?**  
  **A**: In standard Trie, single-child paths store 1 character per node (e.g. `r -> o -> m -> a -> n`). A **Radix Tree** compresses contiguous single-child chains into a single node storing `"roman"`, saving massive pointer overhead in memory-constrained systems (e.g., Linux routing tables, IP lookups).


---

## 9. Tags & Related Problems

- **Tags**: `Trie`, `Prefix Tree`, `Design`, `String Data Structures`, `LeetCode-208`, `Medium`
- **Related problems to practice next**:
- **Implement Trie II**: Frequency and erase operations.
- **Longest Word with All Prefixes**: Trie completeness check.
