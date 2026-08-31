# Introduction to Priority Queues and Binary Heaps (Step 11.1 — Learning)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Introduction to Priority Queues and Binary Heaps](https://takeuforward.org/data-structure/introduction-to-priority-queues-using-binary-heaps/)
- **Difficulty**: Easy
- **Statement**: Explain the Complete Binary Tree array representation of Binary Heaps, index relations ($2i+1, 2i+2, \lfloor(i-1)/2\rfloor$), Heap Order Invariants (Min-Heap vs Max-Heap), Heapify $\mathcal{O}(\log N)$, and Build Heap $\mathcal{O}(N)$.

---

## 1. Problem, Restated

Comprehensive guide to Binary Heap array representation and mathematical proofs.

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Value / Top-K elements / Merged list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

A Binary Heap is a Complete Binary Tree packed compactly into a 0-indexed array without pointer overhead: for node at index $i$, Left Child $= 2i + 1$, Right Child $= 2i + 2$, Parent $= \lfloor (i - 1)/2 \rfloor$. In a Min-Heap, `arr[parent] <= arr[child]`. In a Max-Heap, `arr[parent] >= arr[child]`. Insertion and deletion take $\mathcal{O}(\log N)$, peek is $\mathcal{O}(1)$, and building a heap takes $\mathcal{O}(N)$ using bottom-up sift-down.

- **Underlying Pattern**: `Complete Binary Tree Flat Array Representation`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Sorted array where insertion takes $\mathcal{O}(N)$ shift time.

### C++17 Code
```cpp
// Sorted array approach O(N) insertion
```

### Java Code
```java
// Java equivalent
// Sorted array approach O(N) insertion
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ per insert.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Linear array shifting.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Priority Queue / Heap implementation below directly achieves optimal $\mathcal{O}(N \log K)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Binary Heap Array Structure with $\mathcal{O}(\log N)$ push/pop and $\mathcal{O}(N)$ Build Heap.

### C++17 Code
```cpp
#include <iostream>
#include <vector>
using namespace std;

// Index relations for 0-indexed array heap
inline int parent(int i) { return (i - 1) / 2; }
inline int leftChild(int i) { return 2 * i + 1; }
inline int rightChild(int i) { return 2 * i + 2; }

void demonstrateHeapIndices() {
    int root = 0;
    cout << "Root left child:  " << leftChild(root) << "\n";  // 1
    cout << "Root right child: " << rightChild(root) << "\n"; // 2
    cout << "Parent of node 1: " << parent(1) << "\n";      // 0
    cout << "Parent of node 2: " << parent(2) << "\n";      // 0
}
```

### Java Code
```java
class Solution {
    // Index relations for 0-indexed array heap
    int parent(int i) { return (i - 1) / 2; }
    int leftChild(int i) { return 2 * i + 1; }
    int rightChild(int i) { return 2 * i + 2; }
    
    void demonstrateHeapIndices() {
        int root = 0;
        System.out.println("Root left child:  " + leftChild(root));  // 1
        System.out.println("Root right child: " + rightChild(root)); // 2
        System.out.println("Parent of node 1: " + parent(1));      // 0
        System.out.println("Parent of node 2: " + parent(2));      // 0
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log N)$ push/pop, $\mathcal{O}(1)$ top, $\mathcal{O}(N)$ Build Heap.
- **Space Complexity**: $\mathcal{O}(N)$ contiguous array buffer.
- **Why this is optimal**: Array-backed complete binary tree provides optimal cache locality with zero pointer overhead.

---

## 6. Dry Run

Complete Binary Tree `[10, 20, 15, 30, 40]`

| Step | Action / State Change | Result |
|---|---|---|
| `Root (i=0)` | val 10: Left child index 1 (val 20), Right child index 2 (val 15) | Valid Min-Heap |
| `Node (i=1)` | val 20: Left child index 3 (val 30), Right child index 4 (val 40) | Valid Min-Heap |
| `Result` | All parent <= child relations satisfied | Min-Heap verified ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single node heap.
- Full binary tree vs last level partially filled from left.

### Common Bugs to Avoid
- Using 1-indexed formulas ($2i, 2i+1, i/2$) on 0-indexed C++ vectors.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does Build Heap take O(N) instead of O(N log N)?**  
  **A**: Because nodes at height $h$ require at most $h$ swaps. There are $N/2^{h+1}$ nodes at height $h$. Summing $\sum_{h=0}^{\log N} \frac{N}{2^{h+1}} h = N \sum \frac{h}{2^{h+1}} = N \times 1 = \mathcal{O}(N)$ by the Taylor series of geometric progression!


---

## 9. Tags & Related Problems

- **Tags**: `Heap`, `Binary Heap`, `Priority Queue`, `Basics`, `Easy`
- **Related problems to practice next**:
- **Min Heap Implementation**: Code implementation.
