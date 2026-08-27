# Kth Smallest Element in an Array (Step 11.2 — Medium Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Kth Smallest Element in an Array](https://takeuforward.org/data-structure/kth-largest-smallest-element-in-an-array/)
- **Difficulty**: Medium
- **Statement**: Given an array `arr` and an integer $k$, find the $k^{\text{th}}$ smallest element in the array in $\mathcal{O}(N \log K)$ time and $\mathcal{O}(K)$ space.

---

## 1. Problem, Restated

Maintain a Max-Heap of size $k$: after processing all elements, the heap root is the $k^{\text{th}}$ smallest element.

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Value / Top-K elements / Merged list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Maintain a Max-Heap `priority_queue<int>` of capacity $k$. For each element $x$: push $x$ to heap; if heap size exceeds $k$, pop the largest element (`pq.pop()`). The heap retains the $k$ smallest elements, and its root is the LARGEST of these $k$ smallest elements, which is exactly the $k^{\text{th}}$ smallest!

- **Underlying Pattern**: `Max-Heap of Size $k$ (Bottom-K Retaining Filter)`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Sort array in $\mathcal{O}(N \log N)$, return `arr[k - 1]`.

### C++17 Code
```cpp
// Sort approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N)$.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Full sort.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Priority Queue / Heap implementation below directly achieves optimal $\mathcal{O}(N \log K)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Max-Heap of Size $K$ in $\mathcal{O}(N \log K)$ time.

### C++17 Code
```cpp
#include <vector>
#include <queue>
using namespace std;

class Solution {
public:
    int kthSmallest(int arr[], int l, int r, int k) {
        // Max-heap to maintain the k smallest elements
        priority_queue<int> maxHeap;
        
        for (int i = l; i <= r; i++) {
            maxHeap.push(arr[i]);
            if ((int)maxHeap.size() > k) {
                maxHeap.pop(); // discard largest element
            }
        }
        
        return maxHeap.top(); // kth smallest
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log K)$ time.
- **Space Complexity**: $\mathcal{O}(K)$ heap space.
- **Why this is optimal**: Max-heap bounded to $k$ elements.

---

## 6. Dry Run

`arr = [7, 10, 4, 3, 20, 15], k = 3`

| Step | Action / State Change | Result |
|---|---|---|
| `Push 7, 10, 4` | maxHeap: `[10, 7, 4]` (size 3) | top = 10 |
| `Push 3` | maxHeap: `[10, 7, 4, 3]` -> pop 10 -> `[7, 4, 3]` | top = 7 |
| `Push 20` | maxHeap: `[20, 7, 4, 3]` -> pop 20 -> `[7, 4, 3]` | top = 7 |
| `Push 15` | maxHeap: `[15, 7, 4, 3]` -> pop 15 -> `[7, 4, 3]` | top = 7 |
| `Result` | maxHeap.top() = 7 | 3rd Smallest = 7 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $k = 1$ (returns min).
- $k = N$ (returns max).

### Common Bugs to Avoid
- Confusing Min-Heap with Max-Heap (Min-Heap finds Kth Largest; Max-Heap finds Kth Smallest).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is the average time complexity using QuickSelect?**  
  **A**: QuickSelect achieves $\mathcal{O}(N)$ average time, but degrades to $\mathcal{O}(N^2)$ worst case on sorted arrays. The Heap approach guarantees deterministic $\mathcal{O}(N \log K)$ worst-case time.


---

## 9. Tags & Related Problems

- **Tags**: `Heap`, `Priority Queue`, `Top-K`, `Medium`
- **Related problems to practice next**:
- **Kth Largest Element**: Min-Heap counterpart.
