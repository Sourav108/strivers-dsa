# C++ STL Complete Guide (Containers, Iterators, Algorithms) (Step 1.3 — C++ Standard Template Library (STL))

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [C++ STL Complete Guide (Containers, Iterators, Algorithms)](https://takeuforward.org/c/c-stl-tutorial-most-frequent-used-stl-containers/)
- **Difficulty**: Easy
- **Statement**: Master the full suite of modern C++ STL: Sequence Containers (`vector`, `deque`, `list`), Container Adapters (`stack`, `queue`, `priority_queue`), Associative Containers (`set`, `map`, `multiset`), Unordered Containers (`unordered_set`, `unordered_map`), Iterators, and Standard Algorithms (`sort`, `lower_bound`, `upper_bound`, `binary_search`, `next_permutation`, `__builtin_popcount`).

---

## 1. Problem, Restated

Comprehensive operational guide to Standard Template Library data structures, time complexities, iterator invalidation rules, and algorithmic primitives.

- **Input**: Parameters specified.
- **Output**: Result meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

C++ STL separates containers (data storage) from algorithms (operations) via iterators (generalized pointers). Sequence containers provide contiguous or linked access; associative containers use Red-Black Self-Balancing Trees ($\mathcal{O}(\log N)$); unordered containers use Hash Tables (amortized $\mathcal{O}(1)$).

- **Underlying Pattern**: `Generic Programming & Standard Template Library Primitives`.

---

## 3. Approach 1 — Brute Force / Naive

### Idea
Handcrafted array resizing, sorting, and manual binary trees for basic collections.

### C++17 Code
```cpp
// Manual dynamic array and bubble sort
#include <iostream>
using namespace std;

struct ManualVector {
    int* data;
    int size;
    int cap;
    ManualVector() : size(0), cap(2) { data = new int[2]; }
    void push(int x) {
        if (size == cap) {
            cap *= 2;
            int* next = new int[cap];
            for (int i = 0; i < size; i++) next[i] = data[i];
            delete[] data;
            data = next;
        }
        data[size++] = x;
    }
};
```

### Java Code
```java
// Manual dynamic array and bubble sort
static class ManualVector {
    int* data;
    int size;
    int cap;
    public ManualVector() { /* initialized: size(0), cap(2)  */  data = new int[2];  }
    void push(int x) {
        if (size == cap) {
            cap *= 2;
            int* next = new int[cap];
            for (int i = 0; i < size; i++) next[i] = data[i];
            delete[] data;
            data = next;
        }
        data[size++] = x;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ manual allocations.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Prone to memory leaks, non-generic, missing standard compiler vectorization.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard mathematical / optimal algorithmic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
Idiomatic Modern C++17 STL with generic containers, lambda comparators, and standard algorithms.

### C++17 Code
```cpp
#include <iostream>
#include <vector>
#include <deque>
#include <stack>
#include <queue>
#include <set>
#include <map>
#include <unordered_map>
#include <algorithm>
#include <numeric>
using namespace std;

void demonstrateSTL() {
    // 1. Vector: Dynamic array with O(1) amortized push_back
    vector<int> v = {4, 1, 8, 3, 2};
    v.push_back(5);
    sort(v.begin(), v.end()); // O(N log N) Introsort
    
    // 2. Priority Queue: Max-Heap (default) & Min-Heap
    priority_queue<int> maxHeap;
    priority_queue<int, vector<int>, greater<int>> minHeap;
    
    // 3. Map (Red-Black Tree: O(log N)) vs Unordered Map (Hash Table: O(1) avg)
    map<string, int> treeMap;
    unordered_map<string, int> hashMap;
    treeMap["banana"] = 3;
    treeMap["apple"] = 5; // Ordered alphabetically: apple -> banana
    
    // 4. Standard Algorithms: Binary Search & Permutations
    bool exists = binary_search(v.begin(), v.end(), 3);
    auto lb = lower_bound(v.begin(), v.end(), 3); // iterator to first element >= 3
    
    // Builtin Bitwise Intrinsic
    int setBits = __builtin_popcount(29); // 29 = 11101 -> 4 set bits
    
    cout << "Tree Map First Key: " << treeMap.begin()->first << "\n";
    cout << "Binary Search for 3: " << boolalpha << exists << "\n";
    cout << "Set Bits in 29: " << setBits << "\n";
}

int main() {
    demonstrateSTL();
    return 0;
}
```

### Java Code
```java
import java.util.*;

class Solution {
    void demonstrateSTL() {
        // 1. Vector: Dynamic array with O(1) amortized push_back
        int[] v = {4, 1, 8, 3, 2};
        v.add(5);
        Arrays.sort(v); // O(N log N) Introsort
        
        // 2. Priority Queue: Max-Heap (default) & Min-Heap
        PriorityQueue<int[]> maxHeap = new PriorityQueue<>((a, b) . Integer.compare(a[0], b[0]));
        PriorityQueue<int[]> minHeap = new PriorityQueue<>((a, b) . Integer.compare(a[0], b[0]));
        
        // 3. Map (Red-Black Tree: O(log N)) vs Unordered Map (Hash Table: O(1) avg)
        map<String, int> treeMap;
        Map<String, Integer> hashMap = new HashMap<>();
        treeMap["banana"] = 3;
        treeMap["apple"] = 5; // Ordered alphabetically: apple . banana
        
        // 4. Standard Algorithms: Binary Search Permutations
        boolean exists = binary_search(v.begin(), v.end(), 3);
        var lb = lower_bound(v.begin(), v.end(), 3); // iterator to first element >= 3
        
        // Builtin Bitwise Intrinsic
        int setBits = __builtin_popcount(29); // 29 = 11101 . 4 set bits
        
        System.out.println("Tree Map First Key: " + treeMap.begin().first);
        System.out.println("Binary Search for 3: " + boolalpha + exists);
        System.out.println("Set Bits in 29: " + setBits);
    }
    
    public static void main(String[] args) {
        demonstrateSTL();
        return 0;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ to $\mathcal{O}(\log N)$ for standard operations; $\mathcal{O}(N \log N)$ for `std::sort`.
- **Space Complexity**: $\mathcal{O}(N)$ memory with optimal RAII resource cleanup.
- **Why this is optimal**: Guaranteed theoretical asymptotic bounds implemented with cache-friendly contiguous buffers and self-balancing trees.

---

## 6. Dry Run

STL Complexity & Underlying Data Structure Matrix

| Step | Action / State Change | Result |
|---|---|---|
| ``std::vector`` | Dynamic Contiguous Array | Access: $\mathcal{O}(1)$, Push Back: $\mathcal{O}(1)$ amortized |
| ``std::deque`` | Chunked Map of Fixed Buffers | Push/Pop Front & Back: $\mathcal{O}(1)$ |
| ``std::set` / `std::map`` | Red-Black Self-Balancing BST | Search / Insert / Delete: $\mathcal{O}(\log N)$ strictly |
| ``std::unordered_map`` | Separate Chaining Hash Table | Search / Insert: Average $\mathcal{O}(1)$, Worst $\mathcal{O}(N)$ |
| ``std::priority_queue`` | Binary Max-Heap in Array | Top: $\mathcal{O}(1)$, Push / Pop: $\mathcal{O}(\log N)$ |
| ``std::sort`` | Introsort (Quick + Heap + Insertion) | Time: $\mathcal{O}(N \log N)$ guaranteed |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Iterator invalidation during `v.push_back()` (reallocation moves heap buffer $\implies$ existing pointers/iterators become dangling).
- `unordered_map` worst-case collision hash attack $\mathcal{O}(N)$ (mitigated with custom splitmix64 hash).
- Accessing `map[key]` with `[]` operator automatically inserts default value if key is absent.

### Common Bugs to Avoid
- Modifying keys inside `std::set` (keys are `const` to preserve BST invariant).
- Using `map.count()` vs `map.find()` vs `map[key]` (avoid `map[key]` when checking existence without insertion).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is Introsort used by std::sort?**  
  **A**: Introsort starts with QuickSort. If recursion depth exceeds $2 \log_2 N$, it switches to HeapSort to prevent $\mathcal{O}(N^2)$ worst-case. For small arrays ($N \le 16$), it switches to InsertionSort for cache speed.

- **Q2: What causes vector iterator invalidation?**  
  **A**: When `vector` exceeds capacity, it allocates a new larger buffer elsewhere on the heap and deallocates the old buffer. Any existing pointers or iterators still point to the freed memory.

- **Q3: Why is std::priority_queue default a max-heap, and how to create a min-heap?**  
  **A**: Default is `priority_queue<T, vector<T>, less<T>>` (Max-Heap). For Min-Heap, use `priority_queue<T, vector<T>, greater<T>>`.


---

## 9. Tags & Related Problems

- **Tags**: `C++ STL`, `Data Structures`, `Containers`, `Algorithms`, `Easy`
- **Related problems to practice next**:
- **Sorting Techniques**: Introsort details.
- **Learn Basic Hashing**: Unordered map hash tables.
