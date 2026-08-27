# Arrays & 2D Arrays Basics in C++ (Step 1.1 — Things to Know in C++)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Arrays & 2D Arrays Basics in C++](https://takeuforward.org/c/arrays-in-c/)
- **Difficulty**: Easy
- **Statement**: Master 1D and 2D arrays in C++, understanding contiguous memory allocation, row-major layout, pointer decay, stack vs heap allocation, and cache locality.

---

## 1. Problem, Restated

Understand fixed-size contiguous memory buffers, 2D index flattening ($i \times \text{cols} + j$), and array decay to pointers.

---

## 2. Intuition & Pattern

An array is a contiguous block of memory where element $i$ is located at address $\text{base} + i \times \text{sizeof}(T)$. In 2D arrays, C++ uses **Row-Major Order**: rows are stored contiguously one after another. Traversing row-by-row (`matrix[i][j]`) exploits spatial cache locality, whereas column-by-column traversal causes constant CPU cache misses.

- **Underlying Pattern**: `Contiguous Memory Addressing & Cache Locality`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Column-Major Traversal on Row-Major 2D Array: Outer loop on columns, inner loop on rows.

### C++17 Code
```cpp
#include <iostream>
using namespace std;

// Column-major traversal: slow due to cache misses
long long sumColumnMajor(int** mat, int rows, int cols) {
    long long sum = 0;
    for (int j = 0; j < cols; j++) {
        for (int i = 0; i < rows; i++) {
            sum += mat[i][j]; // Cache miss on every iteration for large matrices!
        }
    }
    return sum;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(R \times C)$ with heavy CPU L1/L2 cache misses.
- **Space Complexity**: $\mathcal{O}(1)$ memory.
- **Why it's not good enough**: Accesses non-contiguous memory addresses separated by `cols * sizeof(int)` bytes.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Row-Major Traversal for Maximum Spatial Cache Locality.

### C++17 Code
```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    // 1D Stack Array
    int arr[5] = {10, 20, 30, 40, 50};
    
    // 2D Stack Array (Row-Major: stored contiguously as [1, 2, 3, 4, 5, 6])
    int mat[2][3] = {
        {1, 2, 3},
        {4, 5, 6}
    };
    
    // Cache-friendly row-major traversal
    long long totalSum = 0;
    for (int i = 0; i < 2; i++) {
        for (int j = 0; j < 3; j++) {
            totalSum += mat[i][j]; // Sequential memory access: fits perfectly into CPU cache line
        }
    }
    
    cout << "Total Sum: " << totalSum << "\n";
    
    // Dynamic 2D Array using std::vector (safe heap allocation)
    int rows = 3, cols = 4;
    vector<vector<int>> dynamicMat(rows, vector<int>(cols, 0));
    dynamicMat[1][2] = 42;
    
    cout << "dynamicMat[1][2] = " << dynamicMat[1][2] << "\n";
    
    return 0;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(R \times C)$ with optimal hardware prefetching and cache hits.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Sequentially accesses memory in the exact layout it is stored.

---

## 6. Dry Run & Key Reference

2D Array Address Mapping: `mat[2][3]`

| Step | Action / State Change | Result |
|---|---|---|
| `mat[0][0]` | Base + (0*3 + 0)*4 = Base + 0 | Value 1 |
| `mat[0][1]` | Base + (0*3 + 1)*4 = Base + 4 | Value 2 |
| `mat[0][2]` | Base + (0*3 + 2)*4 = Base + 8 | Value 3 |
| `mat[1][0]` | Base + (1*3 + 0)*4 = Base + 12 | Value 4 (Contiguous with mat[0][2]!) |
| `mat[1][1]` | Base + (1*3 + 1)*4 = Base + 16 | Value 5 |
| `mat[1][2]` | Base + (1*3 + 2)*4 = Base + 20 | Value 6 |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Out-of-bounds indexing (`arr[5]` on array of size 5 -> undefined behavior / segfault).
- Allocating huge arrays on stack (e.g. `int arr[10000000]` causes Stack Overflow; must use heap or global scope).
- Passing array to function: decays to pointer `int*`, losing `sizeof` information.

### Common Bugs to Avoid
- Returning a local stack array pointer from a function (dangling pointer).
- Assuming `sizeof(arr)` inside a function gives array size (it gives pointer size 8 bytes).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does an array decay to a pointer when passed to a function?**  
  **A**: To avoid costly deep copies of large memory blocks, C++ implicitly converts the array name to a pointer to its first element (`int arr[]` becomes `int* arr`).

- **Q2: Why is global array size limit larger than local array size limit?**  
  **A**: Local variables are stored on the **Call Stack** (typically limited to 1MB–8MB). Global and static variables are stored in the **BSS/Data Segment**, which can occupy gigabytes of heap/virtual memory.

- **Q3: What is a CPU cache line and why does row-major traversal matter?**  
  **A**: CPUs load memory into cache in 64-byte chunks (cache lines). In row-major order, loading `mat[0][0]` pulls `mat[0][1]..mat[0][15]` into L1 cache for free!

- **Q4: How to flatten a 2D array into a 1D array?**  
  **A**: Formula: `1D_index = row * total_cols + col`. To unflatten: `row = index / total_cols, col = index % total_cols`.


---

## 9. Tags & Related Problems

- **Tags**: `C++ Basics`, `Arrays`, `Memory`, `Cache Locality`, `Easy`
- **Related problems to practice next**:
- **User Input / Output**: Array streaming.
- **Strings & Character Arrays**: 1D character buffers.
