# Top K Frequent Elements in an Array (Step 11.3 — Hard Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Top K Frequent Elements in an Array](https://takeuforward.org/data-structure/k-most-frequent-elements/)
- **Difficulty**: Medium
- **Statement**: Given an integer array `nums` and an integer $k$, return the $k$ most frequent elements in $\mathcal{O}(N)$ or $\mathcal{O}(N \log K)$ time.

---

## 1. Problem, Restated

Find top $K$ elements sorted by frequency using Min-Heap or Bucket Sort.

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Value / Top-K elements / Merged list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) Count frequencies in `unordered_map<int, int> freq`. 2) **Min-Heap Approach**: Maintain Min-Heap of `{frequency, value}` of size $k$. Push each element; if size $> k$, pop smallest frequency. Returns top $k$ in $\mathcal{O}(N \log K)$ time. 3) **Bucket Sort Approach**: Create array of buckets where `bucket[f]` holds all numbers with frequency $f$. Traverse from $f = N$ down to $1$ collecting $k$ elements in strict $\mathcal{O}(N)$ time!

- **Underlying Pattern**: `Frequency Hash Map + Min-Heap of Size $K$ / Bucket Sort $\mathcal{O}(N)$`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Sort unique elements by frequency in $\mathcal{O}(N \log N)$ time.

### C++17 Code
```cpp
// O(N log N) full frequency sort
```

### Java Code
```java
// Java equivalent
// O(N log N) full frequency sort
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Sorts all unique frequencies.

---

## 4. Approach 2 — Better

### Idea
Min-Heap of size K in O(N log K) time.

### C++17 Code
```cpp
#include <vector>
#include <unordered_map>
#include <queue>
using namespace std;
vector<int> topKFrequentHeap(vector<int>& nums, int k) {
    unordered_map<int, int> mp;
    for (int x : nums) mp[x]++;
    priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> pq;
    for (auto& [val, count] : mp) {
        pq.push({count, val});
        if (pq.size() > k) pq.pop();
    }
    vector<int> ans;
    while (!pq.empty()) { ans.push_back(pq.top().second); pq.pop(); }
    return ans;
}
```

### Java Code
```java
import java.util.*;

class Solution {
    int[] topKFrequentHeap(int[] nums, int k) {
        Map<Integer, Integer> mp = new HashMap<>();
        for (int x : nums) mp[x]++;
        priority_queue<pair<int, int>, List<int[]>, greater<pair<int, int>>> pq;
        for (var [val, count] : mp) {
            pq.push({count, val});
            if (pq.length > k) pq.pop();
        }
        List<Integer> ans = new ArrayList<>();
        while (!pq.isEmpty()) { ans.add(pq.peek().second); pq.pop(); }
        return ans;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log K)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ space.
- **Why it's still not optimal**: Standard top-K heap.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Bucket Sort by Frequency in strict $\mathcal{O}(N)$ linear time.

### C++17 Code
```cpp
#include <vector>
#include <unordered_map>
using namespace std;

class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        int n = nums.size();
        unordered_map<int, int> countMap;
        for (int x : nums) {
            countMap[x]++;
        }
        
        // Buckets index represents frequency (0 to n)
        vector<vector<int>> buckets(n + 1);
        for (auto& [val, count] : countMap) {
            buckets[count].push_back(val);
        }
        
        vector<int> result;
        result.reserve(k);
        
        // Scan buckets from highest frequency to lowest
        for (int f = n; f >= 1 && (int)result.size() < k; f--) {
            for (int val : buckets[f]) {
                result.push_back(val);
                if ((int)result.size() == k) break;
            }
        }
        
        return result;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    int[] topKFrequent(int[] nums, int k) {
        int n = nums.length;
        Map<Integer, Integer> countMap = new HashMap<>();
        for (int x : nums) {
            countMap[x]++;
        }
        
        // Buckets index represents frequency (0 to n)
        int[][] buckets(n + 1);
        for (var [val, count] : countMap) {
            buckets[count].add(val);
        }
        
        List<Integer> result = new ArrayList<>();
        result.reserve(k);
        
        // Scan buckets from highest frequency to lowest
        for (int f = n; f >= 1 && result.length < k; f--) {
            for (int val : buckets[f]) {
                result.add(val);
                if (result.length == k) break;
            }
        }
        
        return result;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ strict linear time.
- **Space Complexity**: $\mathcal{O}(N)$ bucket array space.
- **Why this is optimal**: Frequency is bounded by $N$, enabling linear bucket placement.

---

## 6. Dry Run

`nums = [1, 1, 1, 2, 2, 3], k = 2`

| Step | Action / State Change | Result |
|---|---|---|
| `Counts` | `{1:3, 2:2, 3:1}` | Frequency map |
| `Buckets` | bucket[3] = `[1]`, bucket[2] = `[2]`, bucket[1] = `[3]` | Buckets filled |
| `Gather k=2` | Take 1 (freq 3), Take 2 (freq 2) | Result = `[1, 2]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $k = N$ (returns all elements).
- Single element array `[1], k=1`.

### Common Bugs to Avoid
- Allocating bucket size $N$ instead of $N + 1$ (frequencies range up to $N$).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: When is Min-Heap preferred over Bucket Sort?**  
  **A**: When the data is arriving as an **online stream** where total count $N$ is unknown, or memory is constrained to $\mathcal{O}(K)$ rather than $\mathcal{O}(N)$.


---

## 9. Tags & Related Problems

- **Tags**: `Heap`, `Bucket Sort`, `Hash Map`, `LeetCode-347`, `Medium`
- **Related problems to practice next**:
- **Kth Largest Element**: Top-K element.
