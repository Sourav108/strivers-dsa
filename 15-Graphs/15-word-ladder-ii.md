# Word Ladder II (Find all shortest transformation sequences) (Step 15.2 — Problems on BFS / DFS)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Word Ladder II (Find all shortest transformation sequences)](https://takeuforward.org/data-structure/word-ladder-ii-shortest-paths/)
- **Difficulty**: Hard
- **Statement**: Given two words, `beginWord` and `endWord`, and a dictionary `wordList`, return all the shortest transformation sequences from `beginWord` to `endWord`, or an empty list if no such sequence exists. Each sequence must start with `beginWord` and end with `endWord` where each adjacent pair differs by exactly 1 character and all intermediate words exist in `wordList`.

---

## 1. Problem, Restated

Find all shortest path sequences using 2 phases: 1) BFS to record each reachable word's exact minimum step distance in `unordered_map<string, int> levelMap`; 2) Backtracking DFS from `endWord` backwards to `beginWord` following strictly decreasing step levels (`levelMap[next] == levelMap[curr] - 1`).

- **Input**: `string beginWord`, `string endWord`, `vector<string>& wordList`.
- **Output**: `vector<vector<string>>` containing all shortest transformation sequences.
- **Constraints**: $1 \le \text{wordList.length} \le 500$, $\text{word.length} \le 5$.

---

## 2. Intuition & Pattern

Storing entire path vectors `vector<string>` inside the BFS queue leads to Memory Limit Exceeded (MLE) on LeetCode! **Optimal 2-Phase Strategy**: 1) **Phase 1 (BFS Forward)**: Standard BFS computes the exact shortest level for every word in `unordered_map<string, int> mpp` (with `mpp[beginWord] = 1`). Stop BFS once `endWord` level is finalized. 2) **Phase 2 (DFS Backward)**: Start from `endWord`. For each 1-letter mutation `prevWord`: if `mpp.count(prevWord) && mpp[prevWord] == mpp[currWord] - 1`, push `prevWord` to path and recurse! When reaching `beginWord`, reverse path and store in results. This decouples distance computation from path generation, completely eliminating MLE/TLE!

- **Underlying Pattern**: `BFS Level-Map Recording + Reverse Backtracking DFS Path Reconstruction`.

---

## 3. Approach 1 — Naive / Path Queue BFS (MLE on LeetCode)

### Idea
Queue storing full vector of strings (paths) for each step (causes MLE on large testcases).

### C++17 Code
```cpp
#include <string>
#include <vector>
#include <unordered_set>
#include <queue>
using namespace std;
// Brute Force Path Queue (Causes MLE on LeetCode)
vector<vector<string>> findLaddersBrute(string beginWord, string endWord, vector<string>& wordList) {
    unordered_set<string> st(wordList.begin(), wordList.end());
    queue<vector<string>> q;
    q.push({beginWord});
    vector<string> usedOnLevel{beginWord};
    int level = 0;
    vector<vector<string>> ans;
    while (!q.empty()) {
        auto vec = q.front(); q.pop();
        if ((int)vec.size() > level) {
            level++;
            for (auto& it : usedOnLevel) st.erase(it);
            usedOnLevel.clear();
        }
        string word = vec.back();
        if (word == endWord) {
            if (ans.empty() || ans[0].size() == vec.size()) ans.push_back(vec);
            continue;
        }
        for (int i = 0; i < (int)word.size(); i++) {
            char original = word[i];
            for (char c = 'a'; c <= 'z'; c++) {
                word[i] = c;
                if (st.count(word)) {
                    vec.push_back(word);
                    q.push(vec);
                    usedOnLevel.push_back(word);
                    vec.pop_back();
                }
            }
            word[i] = original;
        }
    }
    return ans;
}
```

### Java Code
```java
import java.util.*;

class Solution {
    // Brute Force Path Queue (Causes MLE on LeetCode)
    List<List<String>> findLaddersBrute(String beginWord, String endWord, String[] wordList) {
        Set<String> st(wordList.begin(), wordList.end());
        queue<String[]> q;
        q.push({beginWord});
        String[] usedOnLevel{beginWord};
        int level = 0;
        List<List<String>> ans;
        while (!q.isEmpty()) {
            var vec = q.peek(); q.pop();
            if (vec.length > level) {
                level++;
                for (var it : usedOnLevel) st.remove(it);
                usedOnLevel.clear();
            }
            String word = vec.peekLast();
            if (word == endWord) {
                if (ans.isEmpty() || ans[0].size() == vec.length) ans.add(vec);
                continue;
            }
            for (int i = 0; i < word.length; i++) {
                char original = word[i];
                for (char c = 'a'; c <= 'z'; c++) {
                    word[i] = c;
                    if (st.contains(word)) {
                        vec.add(word);
                        q.push(vec);
                        usedOnLevel.add(word);
                        vec.remove();
                    }
                }
                word[i] = original;
            }
        }
        return ans;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times L \times 26 + \text{paths} \times L)$ time.
- **Space Complexity**: $\mathcal{O}(\text{Exponential})$ queue memory.
- **Why it's not good enough**: Queue stores duplicate paths, exceeding memory limits.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard BFS Level-Map + Reverse DFS algorithm below directly achieves optimal $\mathcal{O}(N \times L \times 26 + K \times L)$ bounds without Memory Limit Exceeded.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (BFS Map + DFS Backtracking)

### Idea
BFS Level-Map + Reverse Backtracking DFS in $\mathcal{O}(N \times L \times 26 + K \times L)$ time and $\mathcal{O}(N \times L)$ space.

### C++17 Code
```cpp
#include <string>
#include <vector>
#include <unordered_set>
#include <unordered_map>
#include <queue>
#include <algorithm>
using namespace std;

class Solution {
private:
    unordered_map<string, int> levelMap;
    vector<vector<string>> ans;
    string bWord;
    
    // Phase 2: Backtracking DFS from endWord backwards to beginWord
    void dfs(string word, vector<string>& path) {
        if (word == bWord) {
            vector<string> fullPath = path;
            reverse(fullPath.begin(), fullPath.end());
            ans.push_back(fullPath);
            return;
        }
        
        int currSteps = levelMap[word];
        
        // Explore all valid predecessor words on (currSteps - 1) level
        for (int i = 0; i < (int)word.length(); i++) {
            char original = word[i];
            for (char ch = 'a'; ch <= 'z'; ch++) {
                word[i] = ch;
                
                if (levelMap.count(word) && levelMap[word] == currSteps - 1) {
                    path.push_back(word);
                    dfs(word, path);
                    path.pop_back(); // Backtrack
                }
            }
            word[i] = original;
        }
    }

public:
    vector<vector<string>> findLadders(string beginWord, string endWord, vector<string>& wordList) {
        unordered_set<string> dict(wordList.begin(), wordList.end());
        if (!dict.count(endWord)) return {};
        
        bWord = beginWord;
        levelMap.clear();
        ans.clear();
        
        // Phase 1: Forward BFS to construct Level Distance Map
        queue<string> q;
        q.push(beginWord);
        levelMap[beginWord] = 1;
        dict.erase(beginWord);
        
        while (!q.empty()) {
            string word = q.front();
            q.pop();
            int steps = levelMap[word];
            
            if (word == endWord) break; // Reached destination level
            
            for (int i = 0; i < (int)word.length(); i++) {
                char original = word[i];
                for (char ch = 'a'; ch <= 'z'; ch++) {
                    word[i] = ch;
                    
                    if (dict.count(word)) {
                        q.push(word);
                        dict.erase(word);
                        levelMap[word] = steps + 1;
                    }
                }
                word[i] = original;
            }
        }
        
        // If endWord was reached during BFS, launch Phase 2 DFS
        if (levelMap.count(endWord)) {
            vector<string> path{endWord};
            dfs(endWord, path);
        }
        
        return ans;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    Map<String, Integer> levelMap = new HashMap<>();
    List<List<String>> ans;
    String bWord;
    
    // Phase 2: Backtracking DFS from endWord backwards to beginWord
    void dfs(String word, String[] path) {
        if (word == bWord) {
            String[] fullPath = path;
            reverse(fullPath.begin(), fullPath.end());
            ans.add(fullPath);
            return;
        }
        
        int currSteps = levelMap[word];
        
        // Explore all valid predecessor words on (currSteps - 1) level
        for (int i = 0; i < word.length(); i++) {
            char original = word[i];
            for (char ch = 'a'; ch <= 'z'; ch++) {
                word[i] = ch;
                
                if (levelMap.contains(word) && levelMap[word] == currSteps - 1) {
                    path.add(word);
                    dfs(word, path);
                    path.remove(); // Backtrack
                }
            }
            word[i] = original;
        }
    }

    List<List<String>> findLadders(String beginWord, String endWord, String[] wordList) {
        Set<String> dict(wordList.begin(), wordList.end());
        if (!dict.contains(endWord)) return {};
        
        bWord = beginWord;
        levelMap.clear();
        ans.clear();
        
        // Phase 1: Forward BFS to construct Level Distance Map
        queue<String> q;
        q.push(beginWord);
        levelMap[beginWord] = 1;
        dict.remove(beginWord);
        
        while (!q.isEmpty()) {
            String word = q.peek();
            q.pop();
            int steps = levelMap[word];
            
            if (word == endWord) break; // Reached destination level
            
            for (int i = 0; i < word.length(); i++) {
                char original = word[i];
                for (char ch = 'a'; ch <= 'z'; ch++) {
                    word[i] = ch;
                    
                    if (dict.contains(word)) {
                        q.push(word);
                        dict.remove(word);
                        levelMap[word] = steps + 1;
                    }
                }
                word[i] = original;
            }
        }
        
        // If endWord was reached during BFS, launch Phase 2 DFS
        if (levelMap.contains(endWord)) {
            String[] path{endWord};
            dfs(endWord, path);
        }
        
        return ans;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times L \times 26 + K \times L)$ time where $K$ is the number of shortest paths.
- **Space Complexity**: $\mathcal{O}(N \times L)$ level map and call stack space (strictly avoiding queue path explosions).
- **Why this is optimal**: BFS step-distance caching ensures DFS only traverses edges belonging to optimal shortest paths.

---

## 6. Dry Run

`beginWord = "hit"`, `endWord = "cog"`, `wordList = ["hot","dot","dog","lot","log","cog"]`

| Step | Action / State Change | Result |
|---|---|---|
| `Phase 1: BFS` | Computes: hit(1), hot(2), dot(3), lot(3), dog(4), log(4), cog(5) | levelMap populated |
| `Phase 2: DFS from cog(5)` | Valid predecessors with step 4: `"dog"` and `"log"` | path: `["cog"]` |
| `Branch 1: dog(4)` | Predecessor with step 3: `"dot"` -> hot(2) -> hit(1) -> Path 1 Found! | `hit -> hot -> dot -> dog -> cog` |
| `Branch 2: log(4)` | Predecessor with step 3: `"lot"` -> hot(2) -> hit(1) -> Path 2 Found! | `hit -> hot -> lot -> log -> cog` |
| `Result` | 2 Shortest Paths of Length 5 returned | All Shortest Paths Reconstructed ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `endWord` not reachable (returns `{}`).
- Multiple paths converging at intermediate word.

### Common Bugs to Avoid
- Erasing `word` from `dict` in forward BFS without allowing same-level multi-parent references if relying on direct parent graphs.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is DFS backward from endWord preferred over DFS forward from beginWord?**  
  **A**: Because the search graph typically has multiple dead-end branches spreading forward from `beginWord`. Searching backward from `endWord` strictly follows valid paths that lead directly to the target, pruning all dead branches!

- **Q2: Why does the naive BFS path queue cause Memory Limit Exceeded (MLE)?**  
  **A**: Because if $M$ paths exist of length $L$, the queue must store $M \times L$ total strings simultaneously. In dense graphs, $M$ can exceed $10^5$, leading to hundreds of megabytes of RAM consumption. The Level-Map approach stores only 1 integer per word!

- **Q3: Can Word Ladder II be solved with Bidirectional BFS?**  
  **A**: Yes! Construct a predecessor DAG during bidirectional BFS, then run backtracking DFS on the combined graph.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `BFS`, `DFS`, `Backtracking`, `LeetCode-126`, `Hard`
- **Related problems to practice next**:
- **Word Ladder I**: Length-only search.
- **Shortest Path in Binary Matrix**: Grid path finding.
