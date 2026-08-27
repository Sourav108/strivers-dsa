# Minimum Cost to Connect Sticks (Huffman coding principle) (Step 11.3 — Hard Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Minimum Cost to Connect Sticks (Huffman coding principle)](https://takeuforward.org/data-structure/minimum-cost-to-connect-sticks/)
- **Difficulty**: Easy
- **Statement**: You have some number of sticks with positive integer lengths. You can connect any two sticks of lengths $x$ and $y$ by paying a cost of $x + y$. Return the minimum cost to connect all sticks into one stick.

---

## 1. Problem, Restated

Greedy Min-Heap simulation: repeatedly merge the two smallest sticks (Huffman Optimal Tree Coding).

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Value / Top-K elements / Merged list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Earlier merged sticks contribute to the total cost repeatedly in every subsequent merge. To MINIMIZE total cost, smaller sticks must be merged first (giving them greater depth in the merge tree) and larger sticks last. Push all sticks into a Min-Heap. While heap size $> 1$: pop two smallest sticks $a$ and $b$, add $(a + b)$ to total cost, and push $(a + b)$ back into the Min-Heap!

- **Underlying Pattern**: `Huffman Greedy Merge with Min-Heap`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Repeated sorting on array after each merge in $\mathcal{O}(N^2 \log N)$ time.

### C++17 Code
```cpp
// O(N^2 log N) sort
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2 \log N)$.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Sorts on every step.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Priority Queue / Heap implementation below directly achieves optimal $\mathcal{O}(N \log K)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Min-Heap Huffman Coding in $\mathcal{O}(N \log N)$ time and $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <vector>
#include <queue>
using namespace std;

class Solution {
public:
    int connectSticks(vector<int>& sticks) {
        // Min-heap
        priority_queue<int, vector<int>, greater<int>> minHeap(sticks.begin(), sticks.end());
        
        int totalCost = 0;
        
        // Repeatedly connect the two shortest sticks
        while (minHeap.size() > 1) {
            int first = minHeap.top(); minHeap.pop();
            int second = minHeap.top(); minHeap.pop();
            
            int cost = first + second;
            totalCost += cost;
            
            minHeap.push(cost);
        }
        
        return totalCost;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N)$ time ($N-1$ merges $\times \log N$ heap operations).
- **Space Complexity**: $\mathcal{O}(N)$ heap space.
- **Why this is optimal**: Min-heap always retrieves the two global minimum lengths in $\mathcal{O}(\log N)$ time.

---

## 6. Dry Run

`sticks = [2, 4, 3]`

| Step | Action / State Change | Result |
|---|---|---|
| `minHeap` | `[2, 3, 4]` | Initialized |
| `Merge 1` | pop 2, 3 -> cost = 5, total = 5 -> push 5 -> minHeap: `[4, 5]` | total = 5 |
| `Merge 2` | pop 4, 5 -> cost = 9, total = 5 + 9 = 14 -> push 9 | total = 14 |
| `Result` | Single stick remaining | Min Cost = 14 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single stick `[5]` (cost is 0, no connections needed).
- All sticks equal length.

### Common Bugs to Avoid
- Forgetting `minHeap.size() > 1` loop condition.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is this algorithm provably optimal?**  
  **A**: This is mathematically isomorphic to **Huffman's Optimal Prefix Tree Coding Algorithm**, proven optimal via the greedy-choice property and optimal substructure theorem.


---

## 9. Tags & Related Problems

- **Tags**: `Heap`, `Greedy`, `Huffman Coding`, `LeetCode-1167`, `Easy`
- **Related problems to practice next**:
- **Kth Largest Element**: Min-heap.
