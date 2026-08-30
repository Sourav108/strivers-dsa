# Kth Largest Element in a Stream (Step 11.3 — Hard Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Kth Largest Element in a Stream](https://takeuforward.org/data-structure/kth-largest-element-in-a-stream/)
- **Difficulty**: Easy
- **Statement**: Design a class to find the $k^{\text{th}}$ largest element in a stream. `KthLargest(int k, int[] nums)` initializes object, and `add(int val)` appends `val` and returns $k^{\text{th}}$ largest.

---

## 1. Problem, Restated

Maintain a Min-Heap of size $k$ across dynamic streaming inputs in $\mathcal{O}(\log K)$ time per call.

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Value / Top-K elements / Merged list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Maintain a Min-Heap of size $k$. `add(val)`: push `val` into Min-Heap. If heap size exceeds $k$, pop the minimum element (`pq.pop()`). Return `pq.top()`. The root always holds the $k^{\text{th}}$ largest element in $\mathcal{O}(\log K)$ time!

- **Underlying Pattern**: `Streaming Min-Heap of Capacity $K$`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Append and sort vector on every `add` in $\mathcal{O}(N \log N)$ time.

### C++17 Code
```cpp
// O(N log N) sort
```

### Java Code
```java
// Java equivalent
// O(N log N) sort
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Full sort.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Priority Queue / Heap implementation below directly achieves optimal $\mathcal{O}(N \log K)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Min-Heap of Size $K$ in $\mathcal{O}(\log K)$ time per `add`.

### C++17 Code
```cpp
#include <vector>
#include <queue>
using namespace std;

class KthLargest {
private:
    int kSize;
    priority_queue<int, vector<int>, greater<int>> minHeap;

public:
    KthLargest(int k, vector<int>& nums) : kSize(k) {
        for (int x : nums) {
            add(x);
        }
    }
    
    int add(int val) {
        minHeap.push(val);
        if ((int)minHeap.size() > kSize) {
            minHeap.pop();
        }
        return minHeap.top();
    }
};
```

### Java Code
```java
class KthLargest {

    int kSize;
    priority_queue<int, int[], greater<int>> minHeap;

    public KthLargest(int k, int[] nums) { /* initialized: kSize(k)  */ 
        for (int x : nums) {
            add(x);
         }
    }
    
    int add(int val) {
        minHeap.push(val);
        if (minHeap.length > kSize) {
            minHeap.pop();
        }
        return minHeap.peek();
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log K)$ per `add()` call.
- **Space Complexity**: $\mathcal{O}(K)$ heap space.
- **Why this is optimal**: Heap size never exceeds $k$.

---

## 6. Dry Run

$k = 3, nums = [4, 5, 8, 2]$ -> stream `add(3), add(5), add(10), add(9), add(4)`

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | minHeap: `[4, 5, 8]` (size 3) | top = 4 |
| `add(3)` | push 3 -> pop 3 -> `[4, 5, 8]` | returns 4 |
| `add(5)` | push 5 -> pop 4 -> `[5, 5, 8]` | returns 5 |
| `add(10)` | push 10 -> pop 5 -> `[5, 8, 10]` | returns 5 |
| `add(9)` | push 9 -> pop 5 -> `[8, 9, 10]` | returns 8 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Initial `nums` size smaller than $k$.
- Duplicate stream inputs.

### Common Bugs to Avoid
- Popping before checking if `size > k`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is Min-Heap size bounded by K?**  
  **A**: Because we only care about the $k$ largest elements. Any element smaller than the current $k^{\text{th}}$ largest can never become the $k^{\text{th}}$ largest in future additions.


---

## 9. Tags & Related Problems

- **Tags**: `Heap`, `Priority Queue`, `Design`, `LeetCode-703`, `Easy`
- **Related problems to practice next**:
- **Kth Largest Element in an Array**: Array version.
