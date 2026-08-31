# Maximum XOR With an Element From Array (Offline Query Sorting + Trie) (Step 17.1 — Theory & Practice)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Maximum XOR With an Element From Array (Offline Query Sorting + Trie)](https://takeuforward.org/data-structure/maximum-xor-queries-trie/)
- **Difficulty**: Hard
- **Statement**: You are given an array `nums` consisting of non-negative integers. You are also given a `queries` array, where `queries[i] = [x_i, m_i]`. The answer to the $i$-th query is the maximum bitwise XOR value of $x_i$ with any element of `nums` that does not exceed $m_i$. In other words, the answer is $\max (x_i \oplus \text{nums}[j])$ for all $j$ such that $\text{nums}[j] \le m_i$. If all elements in `nums` are larger than $m_i$, then the answer is $-1$. Return an integer array `ans` where `ans[i]` is the answer to the $i$-th query.

---

## 1. Problem, Restated

Process $Q$ bounded XOR queries $(x_i, m_i)$ maximizing $x_i \oplus nums[j]$ with $nums[j] \le m_i$ by sorting `nums` and `queries` offline and progressively inserting valid elements into a 32-bit Binary Trie in $\mathcal{O}((N + Q) \log (N + Q) + 32(N + Q))$ time.

- **Input**: Array `nums` and array of queries `queries`.
- **Output**: Array of maximum XOR values.
- **Complexity Goal**: $\mathcal{O}((N + Q) \log (N + Q) + 32(N + Q))$ time.

---

## 2. Intuition & Pattern

**Why Online Trie Insertion Fails**: 
Rebuilding a Trie per query takes $\mathcal{O}(Q \times N)$, causing TLE ($10^5 \times 10^5 = 10^{10}$). 
**Offline Query Transformation**: 
1) **Sort `nums`** in ascending order: `sort(nums.begin(), nums.end())`. 
2) **Sort `queries`** in ascending order of their upper bound $m_i$, while preserving original query indices: `(m_i, x_i, queryIndex)`. 
3) Maintain a single global **Binary Trie** and a pointer `idx = 0` into `nums`: 
   - For each sorted query $(m_i, x_i, qIdx)$: 
     - While `idx < n && nums[idx] <= m_i`: insert `nums[idx]` into the Trie, then `idx++`! 
     - If the Trie is empty (`idx == 0`), no element is $\le m_i \implies \text{ans}[qIdx] = -1$. 
     - Otherwise, query `trie.getMax(x_i)` in $\mathcal{O}(32)$ and store in `ans[qIdx]`! 
4) Return `ans`. 
**Complexity**: Every number is inserted into the Trie **EXACTLY ONCE**. Total time $\mathcal{O}(N \log N + Q \log Q + 32(N + Q))$ and $\mathcal{O}(32N + Q)$ space!

- **Underlying Pattern**: `Offline Query Processing + Monotonic Insertion into Binary Trie`.

---

## 3. Approach 1 — Naive (Linear Scan Per Query)

### Idea
For each query, iterate through all $N$ elements and compute maximum XOR in $\mathcal{O}(Q \times N)$ time.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class SolutionNaive {
public:
    vector<int> maximizeXor(vector<int>& nums, vector<vector<int>>& queries) {
        vector<int> ans;
        for (const auto& q : queries) {
            int x = q[0], m = q[1], maxVal = -1;
            for (int num : nums) {
                if (num <= m) maxVal = max(maxVal, x ^ num);
            }
            ans.push_back(maxVal);
        }
        return ans;
    }
};
```

### Java Code
```java
class SolutionNaive {

    int[] maximizeXor(int[] nums, int[][] queries) {
        int[] ans;
        for (var q : queries) {
            int x = q[0], m = q[1], maxVal = -1;
            for (int num : nums) {
                if (num <= m) maxVal = Math.max(maxVal, x ^ num);
            }
            ans.add(maxVal);
        }
        return ans;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(Q \times N)$ time (for $N, Q = 10^5$, $\approx 10^{10}$ ops $\implies$ TLE).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why it's not good enough**: Scanning the entire array for each query causes quadratic runtime.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard offline query sorting with Binary Trie below directly achieves optimal $\mathcal{O}((N + Q) \log (N + Q) + 32(N + Q))$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Offline Query Sorting + Monotonic Binary Trie)

### Idea
Offline Query Sorting + Monotonic Binary Trie Insertion in $\mathcal{O}(N \log N + Q \log Q + 32(N + Q))$ time and $\mathcal{O}(32N + Q)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

// 2-Way Binary Trie Node
struct Node {
    Node* links[2];
    
    Node() {
        links[0] = nullptr;
        links[1] = nullptr;
    }
    
    bool containsKey(int bit) {
        return links[bit] != nullptr;
    }
    
    Node* get(int bit) {
        return links[bit];
    }
    
    void put(int bit, Node* node) {
        links[bit] = node;
    }
};

class Trie {
private:
    Node* root;
    
public:
    Trie() {
        root = new Node();
    }
    
    // Inserts a number into the Trie in O(32)
    void insert(int num) {
        Node* node = root;
        for (int i = 31; i >= 0; i--) {
            int bit = (num >> i) & 1;
            if (!node->containsKey(bit)) {
                node->put(bit, new Node());
            }
            node = node->get(bit);
        }
    }
    
    // Finds max XOR with num in O(32)
    int getMax(int num) {
        Node* node = root;
        int maxNum = 0;
        
        for (int i = 31; i >= 0; i--) {
            int bit = (num >> i) & 1;
            int oppositeBit = 1 - bit;
            
            if (node->containsKey(oppositeBit)) {
                maxNum |= (1 << i);
                node = node->get(oppositeBit);
            } else {
                node = node->get(bit);
            }
        }
        
        return maxNum;
    }
};

class Solution {
public:
    vector<int> maximizeXor(vector<int>& nums, vector<vector<int>>& queries) {
        int n = nums.size();
        int q = queries.size();
        
        // Step 1: Sort nums array in ascending order
        sort(nums.begin(), nums.end());
        
        // Step 2: Store queries with original indices and sort by m_i: {m_i, x_i, query_index}
        vector<pair<int, pair<int, int>>> sortedQueries;
        for (int i = 0; i < q; i++) {
            sortedQueries.push_back({queries[i][1], {queries[i][0], i}});
        }
        sort(sortedQueries.begin(), sortedQueries.end());
        
        // Step 3: Process queries offline
        Trie trie;
        vector<int> ans(q, -1);
        int idx = 0; // Pointer into nums array
        
        for (int i = 0; i < q; i++) {
            int m = sortedQueries[i].first;
            int x = sortedQueries[i].second.first;
            int qIndex = sortedQueries[i].second.second;
            
            // Insert all nums <= m into Trie (each num is inserted at most once across all queries!)
            while (idx < n && nums[idx] <= m) {
                trie.insert(nums[idx]);
                idx++;
            }
            
            // If no element was inserted (all nums > m), answer remains -1
            if (idx == 0) {
                ans[qIndex] = -1;
            } else {
                ans[qIndex] = trie.getMax(x);
            }
        }
        
        return ans;
    }
};
```

### Java Code
```java
import java.util.*;

// 2-Way Binary Trie Node
static class Node {
    Node  links[2];
    
    Node() {
        links[0] = null;
        links[1] = null;
    }
    
    boolean containsKey(int bit) {
        return links[bit] != null;
    }
    
    Node  get(int bit) {
        return links[bit];
    }
    
    void put(int bit, Node  node) {
        links[bit] = node;
    }
};

class Trie {

    Node  root;

    Trie() {
        root = new Node();
    }
    
    // Inserts a number into the Trie in O(32)
    void insert(int num) {
        Node  node = root;
        for (int i = 31; i >= 0; i--) {
            int bit = (num >> i) & 1;
            if (!node.containsKey(bit)) {
                node.put(bit, new Node());
            }
            node = node.get(bit);
        }
    }
    
    // Finds max XOR with num in O(32)
    int getMax(int num) {
        Node  node = root;
        int maxNum = 0;
        
        for (int i = 31; i >= 0; i--) {
            int bit = (num >> i) & 1;
            int oppositeBit = 1 - bit;
            
            if (node.containsKey(oppositeBit)) {
                maxNum |= (1 << i);
                node = node.get(oppositeBit);
            } else {
                node = node.get(bit);
            }
        }
        
        return maxNum;
    }
};

class Solution {

    int[] maximizeXor(int[] nums, int[][] queries) {
        int n = nums.length;
        int q = queries.length;
        
        // Step 1: Sort nums array in ascending order
        Arrays.sort(nums);
        
        // Step 2: Store queries with original indices and sort by m_i: {m_i, x_i, query_index}
        List<int[]>> sortedQueries;
        for (int i = 0; i < q; i++) {
            sortedQueries.add({queries[i][1], {queries[i][0], i}});
        }
        Arrays.sort(sortedQueries);
        
        // Step 3: Process queries offline
        Trie trie;
        int[] ans = new int[q];
        int idx = 0; // Pointer into nums array
        
        for (int i = 0; i < q; i++) {
            int m = sortedQueries[i].first;
            int x = sortedQueries[i].second.first;
            int qIndex = sortedQueries[i].second.second;
            
            // Insert all nums <= m into Trie (each num is inserted at most once across all queries!)
            while (idx < n && nums[idx] <= m) {
                trie.add(nums[idx]);
                idx++;
            }
            
            // If no element was inserted (all nums > m), answer remains -1
            if (idx == 0) {
                ans[qIndex] = -1;
            } else {
                ans[qIndex] = trie.getMax(x);
            }
        }
        
        return ans;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N + Q \log Q + 32(N + Q))$ time (Sorts: $\mathcal{O}(N \log N + Q \log Q)$, each element inserted once: $\mathcal{O}(32N)$, each query answered in $\mathcal{O}(32Q)$).
- **Space Complexity**: $\mathcal{O}(32N + Q)$ space (Binary Trie stores at most $N$ elements; `sortedQueries` stores $Q$ tuples).
- **Why this is optimal**: Sorting queries offline guarantees that elements are inserted monotonically into a single shared Trie with zero rollbacks.

---

## 6. Dry Run

`nums = [0, 1, 2, 3, 4]`, `queries = [[3, 1], [1, 3], [5, 6]]`

| Step | Action / State Change | Result |
|---|---|---|
| `Sorted Queries` | q0: `(m=1, x=3, idx=0)`; q1: `(m=3, x=1, idx=1)`; q2: `(m=6, x=5, idx=2)` | Sorted by m |
| `Query 0 (m=1, x=3)` | Insert nums <= 1: insert(0), insert(1). Pointer idx = 2. Query trie.getMax(3) $\implies 3 \oplus 0 = 3$ | ans[0] = 3 |
| `Query 1 (m=3, x=1)` | Insert nums <= 3: insert(2), insert(3). Pointer idx = 4. Query trie.getMax(1) $\implies 1 \oplus 2 = 3$ | ans[1] = 3 |
| `Query 2 (m=6, x=5)` | Insert nums <= 6: insert(4). Pointer idx = 5 (all inserted). Query trie.getMax(5) $\implies 5 \oplus 2 = 7$ | ans[2] = 7 |
| `Result` | Return `ans = [3, 3, 7]` | Output = `[3, 3, 7]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $m_i < \min(\text{nums})$ (no elements inserted $\implies$ returns $-1$).
- Single query.
- All elements equal.

### Common Bugs to Avoid
- Putting answer at loop index $i$ instead of original query index `qIndex` (`ans[qIndex] = trie.getMax(x)`).
- Recreating the Trie on each query instead of monotonic pointer extension.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What if queries must be answered ONLINE (cannot be sorted offline)?**  
  **A**: Use a **Persistent Binary Trie**! Each insertion of `nums[i]` creates a new version (allocating only 32 new nodes along the modified branch). For query $(x, m)$, binary search in the sorted array for the largest element $\le m$ to find the version root $R_k$, then query $R_k$ in $\mathcal{O}(32)$ time!

- **Q2: Why does Offline Query Processing achieve optimal linear insertion time?**  
  **A**: Because as $m_i$ increases monotonically, the subset of allowed elements $\{nums[j] \le m_i\}$ grows strictly larger without removing previously valid elements. Thus, a two-pointer approach inserts each array element at most once across all queries!

- **Q3: How does this pattern extend to range XOR queries (e.g. max XOR in subarray nums[L...R])?**  
  **A**: Online range XOR queries on subarrays $[L \dots R]$ are solved using **Persistent Tries** by tracking node subtree sizes and comparing root versions $R_R$ and $R_{L-1}$ in $\mathcal{O}(32)$ time!


---

## 9. Tags & Related Problems

- **Tags**: `Trie`, `Binary Trie`, `Bit Manipulation`, `Offline Queries`, `LeetCode-1707`, `Hard`
- **Related problems to practice next**:
- **Maximum XOR of Two Numbers**: Base problem.
- **Persistent Trie**: Online range queries.
