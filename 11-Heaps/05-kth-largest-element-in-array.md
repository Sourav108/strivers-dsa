# Kth Largest Element in an Array (Step 11.2 — Medium Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Kth Largest Element in an Array](https://takeuforward.org/data-structure/kth-largest-smallest-element-in-an-array/)
- **Difficulty**: Medium
- **Statement**: Given an integer array `nums` and an integer $k$, return the $k^{\text{th}}$ largest element in the array in $\mathcal{O}(N \log K)$ time and $\mathcal{O}(K)$ space.

---

## 1. Problem, Restated

Maintain a Min-Heap of size $k$: after processing all elements, the heap root is the $k^{\text{th}}$ largest element.

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Value / Top-K elements / Merged list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Maintain a Min-Heap `priority_queue<int, vector<int>, greater<int>>` of capacity $k$. For each element $x$: push $x$ to heap; if heap size exceeds $k$, pop the smallest element (`pq.pop()`). At all times, the heap retains the $k$ largest elements seen so far. The root of the Min-Heap is the SMALLEST of these $k$ largest elements, which is exactly the $k^{\text{th}}$ largest element!

- **Underlying Pattern**: `Min-Heap of Size $k$ (Top-K Retaining Filter)`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Sort the entire array in $\mathcal{O}(N \log N)$ time, return `nums[N - k]`.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;
int findKthLargestSort(vector<int>& nums, int k) {
    sort(nums.begin(), nums.end());
    return nums[nums.size() - k];
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Sorts entire array.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Priority Queue / Heap implementation below directly achieves optimal $\mathcal{O}(N \log K)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Min-Heap of Size $K$ in $\mathcal{O}(N \log K)$ time and $\mathcal{O}(K)$ space.

### C++17 Code
```cpp
#include <vector>
#include <queue>
using namespace std;

class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        // Min-heap to maintain the k largest elements
        priority_queue<int, vector<int>, greater<int>> minHeap;
        
        for (int num : nums) {
            minHeap.push(num);
            if ((int)minHeap.size() > k) {
                minHeap.pop(); // discard smallest of the (k+1) elements
            }
        }
        
        return minHeap.top(); // kth largest
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log K)$ time.
- **Space Complexity**: $\mathcal{O}(K)$ heap space.
- **Why this is optimal**: Heap size is strictly bounded by $k$, making each push/pop $\mathcal{O}(\log K)$ instead of $\mathcal{O}(\log N)$.

---

## 6. Dry Run

`nums = [3, 2, 1, 5, 6, 4], k = 2`

| Step | Action / State Change | Result |
|---|---|---|
| `Push 3, 2` | minHeap: `[2, 3]` (size 2) | top = 2 |
| `Push 1` | minHeap: `[1, 2, 3]` -> pop 1 -> `[2, 3]` | top = 2 |
| `Push 5` | minHeap: `[2, 3, 5]` -> pop 2 -> `[3, 5]` | top = 3 |
| `Push 6` | minHeap: `[3, 5, 6]` -> pop 3 -> `[5, 6]` | top = 5 |
| `Push 4` | minHeap: `[4, 5, 6]` -> pop 4 -> `[5, 6]` | top = 5 |
| `Result` | minHeap.top() = 5 | 2nd Largest = 5 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $k = 1$ (returns max).
- $k = N$ (returns min).
- Array with duplicates `[3, 3, 3, 3], k = 2` $\implies 3$.

### Common Bugs to Avoid
- Using Max-Heap of size $N$ (takes $\mathcal{O}(N + K \log N)$ space and memory).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why use a Min-Heap for K-th Largest instead of a Max-Heap?**  
  **A**: Because a Min-Heap lets us keep the $k$ LARGEST elements and easily discard smaller ones in $\mathcal{O}(\log K)$ time, using only $\mathcal{O}(K)$ memory!


---

## 9. Tags & Related Problems

- **Tags**: `Heap`, `Priority Queue`, `Top-K`, `LeetCode-215`, `Medium`
- **Related problems to practice next**:
- **Kth Smallest Element**: Max-Heap counterpart.
- **Top K Frequent Elements**: Frequency top-K.
