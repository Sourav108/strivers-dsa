# Word Ladder I (Shortest transformation sequence length) (Step 15.2 — Problems on BFS / DFS)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Word Ladder I (Shortest transformation sequence length)](https://takeuforward.org/data-structure/word-ladder-i-shortest-paths/)
- **Difficulty**: Hard
- **Statement**: Given two words, `beginWord` and `endWord`, and a dictionary `wordList`, return the number of words in the shortest transformation sequence from `beginWord` to `endWord`, or 0 if no such sequence exists. Every adjacent pair of words must differ by exactly one single character, and each intermediate word must exist in `wordList`.

---

## 1. Problem, Restated

Find shortest path in an unweighted graph where words are vertices and 1-letter mutation differences are edges, using BFS and `unordered_set` in $\mathcal{O}(N \times L \times 26)$ time.

- **Input**: `string beginWord`, `string endWord`, `vector<string>& wordList`.
- **Output**: Integer representing minimum transformation sequence length.
- **Constraints**: $1 \le \text{wordList.length} \le 5000$, $\text{word.length} \le 10$.

---

## 2. Intuition & Pattern

Since all edge weights are 1, **BFS guarantees finding the shortest transformation sequence**! 1) Insert all `wordList` words into `unordered_set<string> dict`. 2) Queue stores `{word, steps}` starting with `{beginWord, 1}`. 3) Pop `currWord`. For each position $i$ from $0$ to $L-1$: replace `currWord[i]` with every character `'a'` to `'z'`. If mutated word matches `endWord`, return `steps + 1`! If mutated word exists in `dict`, push `{mutatedWord, steps + 1}` to queue and **erase it from `dict` immediately** (erasing upon insertion prevents re-visiting!). If queue becomes empty, return 0.

- **Underlying Pattern**: `BFS on Implicit Word Graph with Character Substitution Neighbor Lookups`.

---

## 3. Approach 1 — Naive / All-Pairs Graph Construction

### Idea
Compare all pairs of words in wordList to construct explicit adjacency graph in $\mathcal{O}(N^2 \times L)$ time, then run BFS.

### C++17 Code
```cpp
// O(N^2 * L) graph construction + BFS
```

### Java Code
```java
// Java equivalent
// O(N^2 * L) graph construction + BFS
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2 \times L)$ time.
- **Space Complexity**: $\mathcal{O}(N^2)$ memory.
- **Why it's not good enough**: Quadratic pair comparison is too slow for $N = 5000$.

---

## 4. Approach 2 — Better

### Idea
Standard Single-Direction BFS with 26-character replacement in O(N x L x 26) time and O(N x L) space.

### C++17 Code
```cpp
#include <string>
#include <vector>
#include <unordered_set>
#include <queue>
using namespace std;

class SolutionSingleBFS {
public:
    int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
        unordered_set<string> dict(wordList.begin(), wordList.end());
        if (!dict.count(endWord)) return 0;
        
        queue<pair<string, int>> q;
        q.push({beginWord, 1});
        dict.erase(beginWord);
        
        while (!q.empty()) {
            auto [word, steps] = q.front();
            q.pop();
            
            if (word == endWord) return steps;
            
            for (int i = 0; i < (int)word.length(); i++) {
                char original = word[i];
                for (char ch = 'a'; ch <= 'z'; ch++) {
                    word[i] = ch;
                    if (dict.count(word)) {
                        dict.erase(word);
                        q.push({word, steps + 1});
                    }
                }
                word[i] = original;
            }
        }
        return 0;
    }
};
```

### Java Code
```java
import java.util.*;

class SolutionSingleBFS {

    int ladderLength(String beginWord, String endWord, String[] wordList) {
        Set<String> dict(wordList.begin(), wordList.end());
        if (!dict.contains(endWord)) return 0;
        
        queue<pair<String, int>> q;
        q.push({beginWord, 1});
        dict.remove(beginWord);
        
        while (!q.isEmpty()) {
            var [word, steps] = q.peek();
            q.pop();
            
            if (word == endWord) return steps;
            
            for (int i = 0; i < word.length(); i++) {
                char original = word[i];
                for (char ch = 'a'; ch <= 'z'; ch++) {
                    word[i] = ch;
                    if (dict.contains(word)) {
                        dict.remove(word);
                        q.push({word, steps + 1});
                    }
                }
                word[i] = original;
            }
        }
        return 0;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times L \times 26)$ time.
- **Space Complexity**: $\mathcal{O}(N \times L)$ space.
- **Why it's still not optimal**: Single-direction BFS expands a large search frontier from one side.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Bidirectional BFS (Frontier Meet-in-the-Middle) in $\mathcal{O}(N \times L \times 26)$ time with dramatically reduced search tree volume.

### C++17 Code
```cpp
#include <string>
#include <vector>
#include <unordered_set>
using namespace std;

class Solution {
public:
    int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
        unordered_set<string> dict(wordList.begin(), wordList.end());
        if (!dict.count(endWord)) return 0;
        
        // Two active search frontiers
        unordered_set<string> beginSet{beginWord};
        unordered_set<string> endSet{endWord};
        
        int steps = 1;
        
        while (!beginSet.empty() && !endSet.empty()) {
            // Always expand the smaller frontier to minimize branching factor
            if (beginSet.size() > endSet.size()) {
                swap(beginSet, endSet);
            }
            
            unordered_set<string> nextSet;
            
            for (string word : beginSet) {
                dict.erase(word);
            }
            
            for (string word : beginSet) {
                for (int i = 0; i < (int)word.length(); i++) {
                    char original = word[i];
                    for (char ch = 'a'; ch <= 'z'; ch++) {
                        word[i] = ch;
                        
                        // If frontiers meet -> shortest path found!
                        if (endSet.count(word)) {
                            return steps + 1;
                        }
                        
                        if (dict.count(word)) {
                            nextSet.insert(word);
                            dict.erase(word);
                        }
                    }
                    word[i] = original;
                }
            }
            
            beginSet = move(nextSet);
            steps++;
        }
        
        return 0;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    int ladderLength(String beginWord, String endWord, String[] wordList) {
        Set<String> dict(wordList.begin(), wordList.end());
        if (!dict.contains(endWord)) return 0;
        
        // Two active search frontiers
        Set<String> beginSet{beginWord};
        Set<String> endSet{endWord};
        
        int steps = 1;
        
        while (!beginSet.isEmpty() && !endSet.isEmpty()) {
            // Always expand the smaller frontier to minimize branching factor
            if (beginSet.length > endSet.length) {
                int temp = beginSet; beginSet = endSet; endSet = temp;
            }
            
            Set<String> nextSet = new HashSet<>();
            
            for (String word : beginSet) {
                dict.remove(word);
            }
            
            for (String word : beginSet) {
                for (int i = 0; i < word.length(); i++) {
                    char original = word[i];
                    for (char ch = 'a'; ch <= 'z'; ch++) {
                        word[i] = ch;
                        
                        // If frontiers meet . shortest path found!
                        if (endSet.contains(word)) {
                            return steps + 1;
                        }
                        
                        if (dict.contains(word)) {
                            nextSet.add(word);
                            dict.remove(word);
                        }
                    }
                    word[i] = original;
                }
            }
            
            beginSet = move(nextSet);
            steps++;
        }
        
        return 0;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times L \times 26)$ time (Bidirectional BFS reduces branching from $\mathcal{O}(B^D)$ to $\mathcal{O}(2 \times B^{D/2})$).
- **Space Complexity**: $\mathcal{O}(N \times L)$ hash set and frontier space.
- **Why this is optimal**: Meet-in-the-middle search exponentially trims the BFS frontier width by always expanding the smaller set.

---

## 6. Dry Run

`beginWord = "hit"`, `endWord = "cog"`, `wordList = ["hot","dot","dog","lot","log","cog"]`

| Step | Action / State Change | Result |
|---|---|---|
| `Step 1 (begin="hit")` | Substitute letters: `"hot"` found in dict -> dict.erase("hot"), nextSet: `["hot"]` | steps = 1 |
| `Step 2 (begin="hot")` | Substitutions: `"dot"` and `"lot"` found -> dict.erase, nextSet: `["dot", "lot"]` | steps = 2 |
| `Step 3 (begin="dot","lot")` | From "dot": `"dog"` found. From "lot": `"log"` found -> nextSet: `["dog", "log"]` | steps = 3 |
| `Step 4 (begin="dog","log")` | From "dog": `"cog"` matches endSet! $\implies$ Return steps + 1 = 5 | Shortest Transformation = 5 ✅ (`hit -> hot -> dot -> dog -> cog`) |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `endWord` not in `wordList` (returns 0 immediately).
- `beginWord` equals `endWord`.

### Common Bugs to Avoid
- Erasing from `dict` after processing the level instead of when discovering the word (leads to multiple duplicate entries for words reached through different parents).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is replacing 26 characters faster than comparing with all words in wordList?**  
  **A**: Generating all 1-character mutations takes $L \times 26$ operations with $\mathcal{O}(L)$ hash lookups $\implies \mathcal{O}(26 L^2)$. Comparing against all $N$ words takes $\mathcal{O}(N \times L)$. For typical inputs ($L \le 10, N = 5000$), $26 \times 10^2 = 2600 \ll 5000 \times 10 = 50000$ operations (20x faster!).

- **Q2: Why does Bidirectional BFS drastically outperform standard BFS?**  
  **A**: In graph search with branching factor $B$ and depth $D$, standard BFS visits $\mathcal{O}(B^D)$ states. Bidirectional BFS searches from both source and destination, meeting at depth $D/2$, reducing states visited to $\mathcal{O}(2 \times B^{D/2})$!

- **Q3: How does Word Ladder II differ from Word Ladder I?**  
  **A**: Word Ladder I only requires the shortest sequence LENGTH (integer). Word Ladder II requires returning ALL shortest transformation PATHS (requires BFS level mapping + backtracking DFS path reconstruction).


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `BFS`, `Bidirectional BFS`, `Hash Set`, `LeetCode-127`, `Hard`
- **Related problems to practice next**:
- **Word Ladder II**: Find all transformation paths.
- **Minimum Genetic Mutation**: Gene string BFS.
