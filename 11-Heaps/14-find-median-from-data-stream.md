# Find Median from Data Stream (Max-Heap + Min-Heap Two Balance) (Step 11.3 — Hard Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Find Median from Data Stream (Max-Heap + Min-Heap Two Balance)](https://takeuforward.org/data-structure/find-median-from-data-stream/)
- **Difficulty**: Hard
- **Statement**: The median is the middle value in an ordered integer list. Implement the `MedianFinder` class supporting `addNum(num)` in $\mathcal{O}(\log N)$ and `findMedian()` in $\mathcal{O}(1)$ time.

---

## 1. Problem, Restated

Balance data stream using dual heaps: Max-Heap (smaller half) and Min-Heap (larger half).

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Value / Top-K elements / Merged list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Divide numbers into two halves: 1) `maxHeap` (left half: holds smaller numbers, top is max of left half). 2) `minHeap` (right half: holds larger numbers, top is min of right half). Maintain invariants: `maxHeap.top() <= minHeap.top()`, and `maxHeap.size()` is either equal to `minHeap.size()` or `minHeap.size() + 1`. Median is either `maxHeap.top()` (if odd) or `(maxHeap.top() + minHeap.top()) / 2.0` (if even) in strict $\mathcal{O}(1)$ time!

- **Underlying Pattern**: `Dual Balanced Heaps (`maxHeap.size() == minHeap.size()` or $+1$)`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Insertion sort into a vector in $\mathcal{O}(N)$ per addition.

### C++17 Code
```cpp
// O(N) insertion into vector
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ per `addNum`.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Linear vector shift.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Priority Queue / Heap implementation below directly achieves optimal $\mathcal{O}(N \log K)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Two Balanced Heaps in $\mathcal{O}(\log N)$ `addNum` and $\mathcal{O}(1)$ `findMedian`.

### C++17 Code
```cpp
#include <queue>
using namespace std;

class MedianFinder {
private:
    // Left half: max-heap (holds smaller values)
    priority_queue<int> maxHeap;
    // Right half: min-heap (holds larger values)
    priority_queue<int, vector<int>, greater<int>> minHeap;

public:
    MedianFinder() {}
    
    void addNum(int num) {
        // Step 1: Add to maxHeap
        if (maxHeap.empty() || num <= maxHeap.top()) {
            maxHeap.push(num);
        } else {
            minHeap.push(num);
        }
        
        // Step 2: Balance sizes so maxHeap has at most 1 more element than minHeap
        if (maxHeap.size() > minHeap.size() + 1) {
            minHeap.push(maxHeap.top());
            maxHeap.pop();
        } else if (minHeap.size() > maxHeap.size()) {
            maxHeap.push(minHeap.top());
            minHeap.pop();
        }
    }
    
    double findMedian() {
        if (maxHeap.size() > minHeap.size()) {
            return maxHeap.top(); // odd total count
        } else {
            return (maxHeap.top() + minHeap.top()) / 2.0; // even count
        }
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log N)$ per `addNum`, strictly $\mathcal{O}(1)$ for `findMedian`.
- **Space Complexity**: $\mathcal{O}(N)$ heap space.
- **Why this is optimal**: Two heaps keep the exact median values at their roots.

---

## 6. Dry Run

Stream: `addNum(1), addNum(2), findMedian(), addNum(3), findMedian()`

| Step | Action / State Change | Result |
|---|---|---|
| `addNum(1)` | maxHeap: `[1]`, minHeap: `[]` | median = 1.0 |
| `addNum(2)` | maxHeap: `[1]`, minHeap: `[2]` | median = (1+2)/2 = 1.5 |
| `addNum(3)` | maxHeap: `[2, 1]`, minHeap: `[3]` | median = 2.0 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Total count is even vs odd.
- Integer overflow on `(a + b) / 2.0` (use `double`).

### Common Bugs to Avoid
- Allowing `minHeap.size() > maxHeap.size()` (breaks odd-count median retrieval from `maxHeap.top()`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Can this pattern be generalized to find arbitrary percentiles (e.g. 99th percentile)?**  
  **A**: Yes! Balance the two heaps with proportional size ratio $k : (100 - k)$. The root of the smaller heap will always track the $k^{\text{th}}$ percentile in $\mathcal{O}(1)$ time.


---

## 9. Tags & Related Problems

- **Tags**: `Heap`, `Design`, `Two Heaps`, `LeetCode-295`, `Hard`
- **Related problems to practice next**:
- **Sliding Window Median**: Windowed variant.
