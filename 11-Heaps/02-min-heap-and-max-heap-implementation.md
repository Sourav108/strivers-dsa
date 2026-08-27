# Min Heap and Max Heap Implementation (Push, Pop, Heapify) (Step 11.1 — Learning)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Min Heap and Max Heap Implementation (Push, Pop, Heapify)](https://takeuforward.org/data-structure/min-heap-and-max-heap-implementation/)
- **Difficulty**: Medium
- **Statement**: Implement a Min-Heap from scratch supporting `push(val)`, `pop()` (extract-min), `top()`, `size()`, and `heapifyDown(i)` in $\mathcal{O}(\log N)$ time per modification.

---

## 1. Problem, Restated

Implement array-backed binary heap with `siftUp` and `siftDown` methods.

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Value / Top-K elements / Merged list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) `push(val)`: Append $val$ at end of array (`heap.push_back(val)`), then `siftUp(size - 1)` (swap with parent while `heap[i] < heap[parent(i)]`). 2) `pop()`: Swap root with last element, pop back, then `siftDown(0)` (swap with smaller child while parent violates heap property).

- **Underlying Pattern**: `Sift-Up (Bubble Up) and Sift-Down (Sink Down) Heap Invariant Restoration`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Sorted vector with binary search insertion in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
// O(N) shift insertion
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ per insert.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Vector insertion memory shifting.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Priority Queue / Heap implementation below directly achieves optimal $\mathcal{O}(N \log K)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Binary Min-Heap with Sift-Up and Sift-Down in $\mathcal{O}(\log N)$ time.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <iostream>
using namespace std;

class MinHeap {
private:
    vector<int> heap;
    
    int parent(int i) { return (i - 1) / 2; }
    int left(int i) { return 2 * i + 1; }
    int right(int i) { return 2 * i + 2; }
    
    void siftUp(int i) {
        while (i > 0 && heap[i] < heap[parent(i)]) {
            swap(heap[i], heap[parent(i)]);
            i = parent(i);
        }
    }
    
    void siftDown(int i) {
        int smallest = i;
        int l = left(i);
        int r = right(i);
        int n = heap.size();
        
        if (l < n && heap[l] < heap[smallest]) smallest = l;
        if (r < n && heap[r] < heap[smallest]) smallest = r;
        
        if (smallest != i) {
            swap(heap[i], heap[smallest]);
            siftDown(smallest); // recurse down
        }
    }

public:
    MinHeap() {}
    
    void push(int val) {
        heap.push_back(val);
        siftUp(heap.size() - 1);
    }
    
    int pop() {
        if (heap.empty()) return -1;
        int rootVal = heap[0];
        heap[0] = heap.back();
        heap.pop_back();
        
        if (!heap.empty()) {
            siftDown(0);
        }
        return rootVal;
    }
    
    int top() {
        if (heap.empty()) return -1;
        return heap[0];
    }
    
    int size() {
        return heap.size();
    }
    
    bool empty() {
        return heap.empty();
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log N)$ for `push` and `pop`, $\mathcal{O}(1)$ for `top`.
- **Space Complexity**: $\mathcal{O}(N)$ heap array space.
- **Why this is optimal**: Tree depth is $\lfloor \log_2 N \rfloor$, bounding swaps to at most $\log N$.

---

## 6. Dry Run

Operations: `push(5), push(3), push(8), push(1), pop()`

| Step | Action / State Change | Result |
|---|---|---|
| `push(5)` | heap: `[5]` | root = 5 |
| `push(3)` | push 3 -> siftUp with 5 -> heap: `[3, 5]` | root = 3 |
| `push(8)` | heap: `[3, 5, 8]` | root = 3 |
| `push(1)` | push 1 -> siftUp with 5 -> siftUp with 3 -> heap: `[1, 3, 8, 5]` | root = 1 |
| `pop()` | pops 1, move 5 to root -> siftDown with 3 -> heap: `[3, 5, 8]` | returns 1 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `pop()` on empty heap.
- `push` when elements are identical.

### Common Bugs to Avoid
- Comparing `l <= n` instead of `l < n` (causes out-of-bounds access).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How to convert this to Max-Heap?**  
  **A**: Invert comparison operators: in `siftUp`, check `heap[i] > heap[parent(i)]`; in `siftDown`, check `heap[l] > heap[largest]`, swapping with the largest child.


---

## 9. Tags & Related Problems

- **Tags**: `Heap`, `Binary Heap`, `Implementation`, `Medium`
- **Related problems to practice next**:
- **Convert Min Heap to Max Heap**: Batch heapify.
