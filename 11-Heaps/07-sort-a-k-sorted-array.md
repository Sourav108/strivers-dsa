# Sort a K-Sorted (Nearly Sorted) Array (Step 11.2 — Medium Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Sort a K-Sorted (Nearly Sorted) Array](https://takeuforward.org/data-structure/sort-a-k-sorted-array/)
- **Difficulty**: Medium
- **Statement**: Given a nearly sorted array where each element is at most $k$ positions away from its target sorted position, sort the array in $\mathcal{O}(N \log K)$ time.

---

## 1. Problem, Restated

Slide a Min-Heap of size $(k + 1)$ across array, popping the minimum to sorted index.

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Value / Top-K elements / Merged list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Since each element is at most $k$ positions away from its sorted index, the element destined for index 0 MUST be within `arr[0..k]`. Push the first $k+1$ elements into a Min-Heap. The minimum element in the heap is guaranteed to be the smallest overall! Pop it into `arr[0]`. Add the next element `arr[k+1]`, pop into `arr[1]`, and repeat in $\mathcal{O}(N \log K)$ time!

- **Underlying Pattern**: `Min-Heap of Window Size $(k + 1)$`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Standard sort in $\mathcal{O}(N \log N)$ time.

### C++17 Code
```cpp
// O(N log N) full sort
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N)$.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Ignores the $k$-sorted locality property.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Priority Queue / Heap implementation below directly achieves optimal $\mathcal{O}(N \log K)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Min-Heap of Size $(K + 1)$ in $\mathcal{O}(N \log K)$ time.

### C++17 Code
```cpp
#include <vector>
#include <queue>
using namespace std;

class Solution {
public:
    vector<int> nearlySorted(int arr[], int num, int K) {
        priority_queue<int, vector<int>, greater<int>> minHeap;
        vector<int> sortedArr;
        sortedArr.reserve(num);
        
        // Push first (K + 1) elements
        for (int i = 0; i <= min(K, num - 1); i++) {
            minHeap.push(arr[i]);
        }
        
        // Process remaining elements
        for (int i = K + 1; i < num; i++) {
            sortedArr.push_back(minHeap.top());
            minHeap.pop();
            minHeap.push(arr[i]);
        }
        
        // Pop remaining elements in heap
        while (!minHeap.empty()) {
            sortedArr.push_back(minHeap.top());
            minHeap.pop();
        }
        
        return sortedArr;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log K)$ time (significantly faster than $\mathcal{O}(N \log N)$ when $k \ll N$).
- **Space Complexity**: $\mathcal{O}(K)$ heap space.
- **Why this is optimal**: Limits heap size to $(k + 1)$ elements.

---

## 6. Dry Run

`arr = [6, 5, 3, 2, 8, 10, 9], k = 3`

| Step | Action / State Change | Result |
|---|---|---|
| `Init (first 4 elements)` | push 6, 5, 3, 2 -> minHeap: `[2, 3, 5, 6]` | top = 2 |
| `i = 4 (8)` | pop 2 -> push 8 -> minHeap: `[3, 5, 6, 8]` | ans: `[2]` |
| `i = 5 (10)` | pop 3 -> push 10 -> minHeap: `[5, 6, 8, 10]` | ans: `[2, 3]` |
| `i = 6 (9)` | pop 5 -> push 9 -> minHeap: `[6, 8, 9, 10]` | ans: `[2, 3, 5]` |
| `Flush heap` | pops 6, 8, 9, 10 | Sorted: `[2, 3, 5, 6, 8, 9, 10]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $k = 0$ (array already sorted).
- $k \ge N$ (standard heapsort).

### Common Bugs to Avoid
- Using heap of size $k$ instead of $(k + 1)$ (misses the $(k+1)^{\text{th}}$ candidate).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why must the initial heap size be (k + 1)?**  
  **A**: Because an element can be shifted up to $k$ positions to the right. Thus the smallest element can be located at index $0, 1, \dots, k$ (which comprises exactly $k + 1$ elements).


---

## 9. Tags & Related Problems

- **Tags**: `Heap`, `Priority Queue`, `Sorting`, `Medium`
- **Related problems to practice next**:
- **Kth Largest Element**: Heap filtering.
