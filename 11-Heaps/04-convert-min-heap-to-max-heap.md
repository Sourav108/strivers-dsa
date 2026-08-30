# Convert Min Heap to Max Heap in O(N) (Step 11.1 — Learning)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Convert Min Heap to Max Heap in O(N)](https://takeuforward.org/data-structure/convert-min-heap-to-max-heap/)
- **Difficulty**: Medium
- **Statement**: Given an array `arr` of size $N$ representing a Min-Heap, convert it into a valid Max-Heap in-place in strict $\mathcal{O}(N)$ time.

---

## 1. Problem, Restated

Run bottom-up `maxHeapifyDown` starting from the last internal parent index $(N-2)/2$ down to 0.

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Value / Top-K elements / Merged list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Simply run `maxHeapify(arr, n, i)` for all internal nodes starting backwards from $i = (N - 2)/2$ down to $0$. Since leaves are trivial 1-element max-heaps, merging them bottom-up builds a valid Max-Heap in strict $\mathcal{O}(N)$ time!

- **Underlying Pattern**: `Bottom-Up Sift-Down Heap Construction (Build Heap $\mathcal{O}(N)$)`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Insert elements one by one into a new max-heap in $\mathcal{O}(N \log N)$ time.

### C++17 Code
```cpp
// O(N log N) insertion into new heap
```

### Java Code
```java
// Java equivalent
// O(N log N) insertion into new heap
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Fails the $\mathcal{O}(N)$ time and in-place requirements.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Priority Queue / Heap implementation below directly achieves optimal $\mathcal{O}(N \log K)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Bottom-Up In-Place Max-Heapify in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
private:
    void maxHeapify(vector<int>& arr, int n, int i) {
        int largest = i;
        int left = 2 * i + 1;
        int right = 2 * i + 2;
        
        if (left < n && arr[left] > arr[largest]) largest = left;
        if (right < n && arr[right] > arr[largest]) largest = right;
        
        if (largest != i) {
            swap(arr[i], arr[largest]);
            maxHeapify(arr, n, largest);
        }
    }

public:
    void convertMinToMaxHeap(vector<int>& arr, int n) {
        // Start from the last internal parent down to root
        for (int i = (n - 2) / 2; i >= 0; i--) {
            maxHeapify(arr, n, i);
        }
    }
};
```

### Java Code
```java
class Solution {

    void maxHeapify(int[] arr, int n, int i) {
        int largest = i;
        int left = 2 * i + 1;
        int right = 2 * i + 2;
        
        if (left < n && arr[left] > arr[largest]) largest = left;
        if (right < n && arr[right] > arr[largest]) largest = right;
        
        if (largest != i) {
            int temp = arr[i]; arr[i] = arr[largest]; arr[largest] = temp;
            maxHeapify(arr, n, largest);
        }
    }

    void convertMinToMaxHeap(int[] arr, int n) {
        // Start from the last internal parent down to root
        for (int i = (n - 2) / 2; i >= 0; i--) {
            maxHeapify(arr, n, i);
        }
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ strict time.
- **Space Complexity**: $\mathcal{O}(\log N)$ recursion stack space during heapify.
- **Why this is optimal**: Summing node heights $\sum \frac{N}{2^{h+1}} h = \mathcal{O}(N)$ mathematically bounds the total operations to linear time.

---

## 6. Dry Run

`arr = [3, 5, 9, 6, 8, 20, 10, 12, 18, 9]` ($N = 10$)

| Step | Action / State Change | Result |
|---|---|---|
| `Start i = (10-2)/2 = 4` | arr[4]=8 -> heapify with children `arr[9]=9` -> swap | arr[4]=9, arr[9]=8 |
| `i = 3` | arr[3]=6 -> heapify with `arr[7]=12, arr[8]=18` -> swap with 18 | arr[3]=18 |
| `i = 2, 1, 0` | heapify down | Root becomes max 20 |
| `Result` | Valid Max-Heap `[20, 18, 10, 12, 9, 9, 3, 5, 6, 8]` | Max-Heapified ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 1$.
- Already a max-heap.

### Common Bugs to Avoid
- Starting loop from 0 to $(N-2)/2$ forward instead of backwards down to 0 (forward heapify does not guarantee subtrees are valid max heaps).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why must the loop run backwards from (N-2)/2 down to 0?**  
  **A**: Because `maxHeapify` assumes that both the left and right subtrees are ALREADY valid Max-Heaps. Processing bottom-up guarantees this invariant holds for every parent node!


---

## 9. Tags & Related Problems

- **Tags**: `Heap`, `Binary Heap`, `Maths`, `Medium`
- **Related problems to practice next**:
- **Min Heap Implementation**: Sift down.
