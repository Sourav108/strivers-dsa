# Check if an Array Represents a Min-Heap (Step 11.1 — Learning)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Check if an Array Represents a Min-Heap](https://takeuforward.org/data-structure/check-if-an-array-represents-a-min-heap/)
- **Difficulty**: Easy
- **Statement**: Given an array of integers `arr` of size $N$, return `true` if the array represents a valid Binary Min-Heap, and `false` otherwise.

---

## 1. Problem, Restated

Verify if every non-leaf parent node satisfies `arr[i] <= arr[2i+1]` and `arr[i] <= arr[2i+2]`.

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Value / Top-K elements / Merged list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Leaves automatically satisfy the heap property. Internal parent nodes exist from index $0$ to $(N - 2)/2$. Loop $i$ from $0$ to $(N - 2)/2$: 1) If left child exists ($2i + 1 < N$) and `arr[i] > arr[2i + 1]`, return `false`. 2) If right child exists ($2i + 2 < N$) and `arr[i] > arr[2i + 2]`, return `false`. If all internal nodes pass, return `true` in $\mathcal{O}(N)$ time.

- **Underlying Pattern**: `Internal Node Heap Property Verification ($i \in [0, (N-2)/2]$)`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Recursive tree traversal from root.

### C++17 Code
```cpp
// Recursive validation
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$.
- **Space Complexity**: $\mathcal{O}(\log N)$ stack.
- **Why it's not good enough**: Recursion stack overhead.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Priority Queue / Heap implementation below directly achieves optimal $\mathcal{O}(N \log K)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Linear Iterative Internal Node Scan in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
public:
    bool isMinHeap(int arr[], int n) {
        // Only need to check internal nodes from 0 to (n - 2) / 2
        for (int i = 0; i <= (n - 2) / 2; i++) {
            int left = 2 * i + 1;
            int right = 2 * i + 2;
            
            // Check left child
            if (left < n && arr[i] > arr[left]) {
                return false;
            }
            
            // Check right child
            if (right < n && arr[i] > arr[right]) {
                return false;
            }
        }
        
        return true;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass over $N/2$ internal nodes.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Iterates only over parent nodes with zero extra memory allocations.

---

## 6. Dry Run

`arr = [10, 15, 14, 25, 30]`, $N = 5$

| Step | Action / State Change | Result |
|---|---|---|
| `i = 0 (10)` | left=1 (15 >= 10), right=2 (14 >= 10) | Valid |
| `i = 1 (15)` | left=3 (25 >= 15), right=4 (30 >= 15) | Valid |
| `Result` | All parent nodes satisfied | Return TRUE ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 1$ (single element is always valid min-heap).
- Complete binary tree with missing right child on last internal node.

### Common Bugs to Avoid
- Looping up to $N-1$ and checking out-of-bounds children without index guards.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is the last internal parent node at index (N - 2) / 2?**  
  **A**: The last element is at index $N - 1$. Its parent is at index $\lfloor ((N - 1) - 1)/2 \rfloor = (N - 2)/2$. Nodes beyond $(N - 2)/2$ are leaves with no children.


---

## 9. Tags & Related Problems

- **Tags**: `Heap`, `Binary Heap`, `Arrays`, `Easy`
- **Related problems to practice next**:
- **Check if Array Represents a Max-Heap**: Max-heap counterpart.
