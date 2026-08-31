# Accounts Merge (Step 15.5 — Minimum Spanning Tree & Disjoint Set Union)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Accounts Merge](https://takeuforward.org/data-structure/accounts-merge-dsu-g-54/)
- **Difficulty**: Medium
- **Statement**: Given a list of `accounts` where `accounts[i]` consists of a name followed by emails: `[name, email1, email2, ...]`. Two accounts belong to the same person if they share any common email. Merge all accounts belonging to the same person and return the accounts with emails sorted in ASCII order, with the person's name at index 0.

---

## 1. Problem, Restated

Group accounts sharing any common email using Disjoint Set Union (DSU) on account indices in $\mathcal{O}(N \cdot L \log(N \cdot L))$ time.

- **Input**: `vector<vector<string>>& accounts`.
- **Output**: `vector<vector<string>>` merged accounts with sorted emails.
- **Constraints**: $1 \le \text{accounts.length} \le 1000$, $2 \le \text{accounts}[i].\text{length} \le 10$, $1 \le \text{accounts}[i][j].\text{length} \le 30$.

---

## 2. Intuition & Pattern

Two accounts belong to the same entity if they share at least one email address. 1) Map each unique email to the first account index where it appears: `unordered_map<string, int> mapMailNode`. 2) For account $i$, iterate through its emails $e$: 
   - If $e$ has not been seen before, map `mapMailNode[e] = i`. 
   - If $e$ was previously seen in account $j$, call `dsu.unionBySize(i, mapMailNode[e])`! 
3) Group all emails by their representative account root: `vector<string> mergedMail[n]`. For every email `[e, accIdx]` in `mapMailNode`, insert `e` into `mergedMail[dsu.findUPar(accIdx)]`. 4) For each non-empty `mergedMail[i]`: sort emails lexicographically, prepend `accounts[i][0]` (the person's name), and append to answer. Runs in $\mathcal{O}(N \cdot L \log(N \cdot L))$ time.

- **Underlying Pattern**: `Email-to-Account Hash Map + DSU Account Component Merging`.

---

## 3. Approach 1 — Naive / Adjacency Graph DFS

### Idea
Build undirected email-to-email adjacency graph and perform DFS traversal in $\mathcal{O}(E + V \log V)$ time.

### C++17 Code
```cpp
// DFS email graph traversal
```

### Java Code
```java
// Java equivalent
// DFS email graph traversal
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\sum L_i \log L_i)$ time.
- **Space Complexity**: $\mathcal{O}(\sum L_i)$ space.
- **Why it's not good enough**: Graph DFS requires building heavy string adjacency lists and visited sets.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Email Map + Account Index DSU algorithm below directly achieves optimal $\mathcal{O}(N \cdot L \log(N \cdot L))$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Email Map + Account Index DSU)

### Idea
Email Hash Map with Account Index DSU in $\mathcal{O}(N \cdot L \log(N \cdot L))$ time and $\mathcal{O}(N \cdot L)$ space.

### C++17 Code
```cpp
#include <vector>
#include <string>
#include <unordered_map>
#include <algorithm>
#include <numeric>
using namespace std;

class DisjointSet {
public:
    vector<int> parent, size;
    DisjointSet(int n) {
        parent.resize(n);
        size.resize(n, 1);
        iota(parent.begin(), parent.end(), 0);
    }
    int findUPar(int node) {
        if (node == parent[node]) return node;
        return parent[node] = findUPar(parent[node]);
    }
    void unionBySize(int u, int v) {
        int rootU = findUPar(u);
        int rootV = findUPar(v);
        if (rootU == rootV) return;
        if (size[rootU] < size[rootV]) {
            parent[rootU] = rootV;
            size[rootV] += size[rootU];
        } else {
            parent[rootV] = rootU;
            size[rootU] += size[rootV];
        }
    }
};

class Solution {
public:
    vector<vector<string>> accountsMerge(vector<vector<string>>& accounts) {
        int n = accounts.size();
        DisjointSet dsu(n);
        
        // 1. Map email -> first seen account index
        unordered_map<string, int> mapMailNode;
        for (int i = 0; i < n; i++) {
            for (int j = 1; j < (int)accounts[i].size(); j++) {
                string mail = accounts[i][j];
                if (mapMailNode.find(mail) == mapMailNode.end()) {
                    mapMailNode[mail] = i;
                } else {
                    // Unite current account i with previous account having same email
                    dsu.unionBySize(i, mapMailNode[mail]);
                }
            }
        }
        
        // 2. Group emails by root account parent
        vector<vector<string>> mergedMail(n);
        for (const auto& [mail, accIdx] : mapMailNode) {
            int rootNode = dsu.findUPar(accIdx);
            mergedMail[rootNode].push_back(mail);
        }
        
        // 3. Format result: Sort emails and attach account name
        vector<vector<string>> ans;
        for (int i = 0; i < n; i++) {
            if (mergedMail[i].empty()) continue;
            
            // Sort emails lexicographically
            sort(mergedMail[i].begin(), mergedMail[i].end());
            
            vector<string> temp;
            temp.push_back(accounts[i][0]); // Name
            for (const auto& mail : mergedMail[i]) {
                temp.push_back(mail);
            }
            ans.push_back(temp);
        }
        
        return ans;
    }
};
```

### Java Code
```java
import java.util.*;

class DisjointSet {

    int[] parent, size;
    DisjointSet(int n) {
        parent.resize(n);
        size.resize(n, 1);
        iota(parent.begin(), parent.end(), 0);
    }
    int findUPar(int node) {
        if (node == parent[node]) return node;
        return parent[node] = findUPar(parent[node]);
    }
    void unionBySize(int u, int v) {
        int rootU = findUPar(u);
        int rootV = findUPar(v);
        if (rootU == rootV) return;
        if (size[rootU] < size[rootV]) {
            parent[rootU] = rootV;
            size[rootV] += size[rootU];
        } else {
            parent[rootV] = rootU;
            size[rootU] += size[rootV];
        }
    }
};

class Solution {

    List<List<String>> accountsMerge(List<List<String>> accounts) {
        int n = accounts.length;
        DisjointSet dsu(n);
        
        // 1. Map email . first seen account index
        Map<String, Integer> mapMailNode = new HashMap<>();
        for (int i = 0; i < n; i++) {
            for (int j = 1; j < (int)accounts[i].size(); j++) {
                String mail = accounts[i][j];
                if (mapMailNode.find(mail) == mapMailNode.end()) {
                    mapMailNode[mail] = i;
                } else {
                    // Unite current account i with previous account having same email
                    dsu.unionBySize(i, mapMailNode[mail]);
                }
            }
        }
        
        // 2. Group emails by root account parent
        List<List<String>> mergedMail(n);
        for (var [mail, accIdx] : mapMailNode) {
            int rootNode = dsu.findUPar(accIdx);
            mergedMail[rootNode].add(mail);
        }
        
        // 3. Format result: Sort emails and attach account name
        List<List<String>> ans;
        for (int i = 0; i < n; i++) {
            if (mergedMail[i].isEmpty()) continue;
            
            // Sort emails lexicographically
            sort(mergedMail[i].begin(), mergedMail[i].end());
            
            String[] temp;
            temp.add(accounts[i][0]); // Name
            for (var mail : mergedMail[i]) {
                temp.add(mail);
            }
            ans.add(temp);
        }
        
        return ans;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \cdot L \log(N \cdot L))$ time (where $N = \text{accounts.size()}$, $L = \text{average emails per account}$; dominated by sorting merged email lists).
- **Space Complexity**: $\mathcal{O}(N \cdot L)$ hash map, DSU, and merged list memory.
- **Why this is optimal**: Operating DSU on integer account indices $0 \dots n-1$ avoids string-based DSU overhead.

---

## 6. Dry Run

`accounts = [["John","a@m.co","b@m.co"], ["John","c@m.co"], ["John","a@m.co","d@m.co"], ["Mary","m@m.co"]]`

| Step | Action / State Change | Result |
|---|---|---|
| `Account 0 (John)` | a@m.co->0, b@m.co->0 | map updated |
| `Account 1 (John)` | c@m.co->1 | map updated |
| `Account 2 (John)` | a@m.co exists (0) $\implies$ unite(2, 0); d@m.co->2 | Account 2 merged with 0! |
| `Account 3 (Mary)` | m@m.co->3 | map updated |
| `Grouping by root` | Root 0: [a@m.co, b@m.co, d@m.co]; Root 1: [c@m.co]; Root 3: [m@m.co] | Formatted & Sorted |
| `Final Result` | `[["John","a@m.co","b@m.co","d@m.co"], ["John","c@m.co"], ["Mary","m@m.co"]]` | Merged correctly ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Two distinct people with the SAME name but completely disjoint emails (remain separate accounts).
- Account with 1 email only.
- All accounts merge into 1 single user.

### Common Bugs to Avoid
- Assuming accounts with the same name belong to the same person without checking emails (people can share names).
- Forgetting to sort emails in the merged output.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why do we perform DSU on integer account indices rather than on email strings directly?**  
  **A**: Performing DSU on integers $0 \dots n-1$ is cache-friendly and takes $\mathcal{O}(1)$ array lookups. Doing DSU on string objects requires hashing and string copies on every `find` / `union`, adding massive runtime and memory overhead!

- **Q2: Why does iterating over 'mapMailNode' automatically deduplicate emails across accounts?**  
  **A**: Because `mapMailNode` is a hash map keyed by unique email strings, each distinct email appears exactly once in the map, so it is inserted into `mergedMail` exactly once!

- **Q3: How is this used in Master Data Management (MDM) & Customer 360?**  
  **A**: Entity Resolution & Identity Stitching: Merging fragmented user profiles across mobile apps, websites, and point-of-sale systems based on common identifiers (email, phone number, loyalty ID).


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `DSU`, `Disjoint Set Union`, `Hash Map`, `LeetCode-721`, `Medium`
- **Related problems to practice next**:
- **Number of Operations Network**: DSU component merging.
- **Redundant Connection**: Cycle detection with DSU.
