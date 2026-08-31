# Functions (Pass by Value & Reference) in C++ (Step 1.1 — Things to Know in C++)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Functions (Pass by Value & Reference) in C++](https://takeuforward.org/c/functions-in-c/)
- **Difficulty**: Easy
- **Statement**: Master C++ functions, call stack frames, parameter passing semantics (Pass by Value, Pass by Pointer, Pass by Reference `&`, Pass by Const Reference `const &`), default arguments, and return value optimization (RVO).

---

## 1. Problem, Restated

Understand memory allocation on the call stack and how parameter passing affects performance and mutation.

---

## 2. Intuition & Pattern

Pass by Value copies the argument into a new stack frame variable (expensive for vectors/strings $\mathcal{O}(N)$). Pass by Reference `T&` creates an alias/pointer to the caller's memory (zero-copy $\mathcal{O}(1)$, allows modification). Pass by Const Reference `const T&` provides zero-copy read-only access, which is the golden standard for passing complex objects in C++.

- **Underlying Pattern**: `Call Stack Frames & Memory Reference Semantics`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Pass by Value for Large Collections: Passing `vector<int>` by value duplicates the entire heap array on every recursive or helper call.

### C++17 Code
```cpp
#include <iostream>
#include <vector>
using namespace std;

// Inefficient: copies entire vector of size N on every function call!
long long sumVectorSlow(vector<int> vec) {
    long long sum = 0;
    for (int x : vec) sum += x;
    return sum;
}
```

### Java Code
```java
class Solution {
    // Inefficient: copies entire vector of size N on every function call!
    long sumVectorSlow(int[] vec) {
        long sum = 0;
        for (int x : vec) sum += x;
        return sum;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ copies per call.
- **Space Complexity**: $\mathcal{O}(N)$ stack/heap allocation.
- **Why it's not good enough**: Deep copy constructor executed on every invocation.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Pass by Reference (`&`) for in-place mutation, `const &` for read-only zero-copy.

### C++17 Code
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

// 1. Pass by Reference: modifies original variables in-place
void swapValues(int& a, int& b) {
    int temp = a;
    a = b;
    b = temp;
}

// 2. Pass by Const Reference: Zero-copy, read-only safety
long long sumVectorFast(const vector<int>& vec) {
    long long total = 0;
    for (int x : vec) total += x;
    return total;
}

int main() {
    int x = 10, y = 20;
    swapValues(x, y);
    cout << "Swapped: x = " << x << ", y = " << y << "\n"; // x=20, y=10
    
    vector<int> data(1000000, 5);
    cout << "Sum: " << sumVectorFast(data) << "\n"; // Zero memory copied!
    
    return 0;
}
```

### Java Code
```java
class Solution {
    // 1. Pass by Reference: modifies original variables in-place
    void swapValues(int a, int b) {
        int temp = a;
        a = b;
        b = temp;
    }
    
    // 2. Pass by Const Reference: Zero-copy, read-only safety
    long sumVectorFast(int[] vec) {
        long total = 0;
        for (int x : vec) total += x;
        return total;
    }
    
    public static void main(String[] args) {
        int x = 10, y = 20;
        swapValues(x, y);
        System.out.println("Swapped: x = " + x + ", y = " + y); // x=20, y=10
        
        int[] data = new int[1000000];
        System.out.println("Sum: " + sumVectorFast(data)); // Zero memory copied!
        
        return 0;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ parameter passing time.
- **Space Complexity**: $\mathcal{O}(1)$ parameter memory overhead.
- **Why this is optimal**: References pass memory addresses (8-byte pointer under the hood) without copying data payloads.

---

## 6. Dry Run & Key Reference

Memory Comparison: Pass by Value vs Pass by Reference

| Step | Action / State Change | Result |
|---|---|---|
| `Pass by Value (`vector<int> v`)` | Allocates new buffer on heap, copies all $N$ elements | Memory: $2\times N$, Time: $\mathcal{O}(N)$ |
| `Pass by Pointer (`vector<int>* v`)` | Passes 8-byte memory address, requires `->` syntax | Memory: 8 bytes, Time: $\mathcal{O}(1)$ |
| `Pass by Reference (`vector<int>& v`)` | Passes alias to original variable, clean `.` syntax | Memory: 8 bytes, Time: $\mathcal{O}(1)$ |
| `Pass by Const Ref (`const vector<int>& v`)` | Zero-copy alias + compiler forbids mutation | Memory: 8 bytes, Time: $\mathcal{O}(1)$ ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Returning a reference to a local stack variable (Dangling Reference $\implies$ undefined behavior).
- Passing literals to non-const reference (`void f(int& x)` -> `f(5)` fails to compile; must use `const int& x`).

### Common Bugs to Avoid
- Accidentally passing recursive function state (like `vector<vector<int>>` or `string`) by value in DFS/Backtracking, causing exponential memory allocations and TLE.
- Returning `const string&` pointing to a temporary object.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is Return Value Optimization (RVO) and Copy Elision?**  
  **A**: RVO is a compiler optimization where the compiler constructs a function's return object directly in the memory location allocated for the caller's target variable, eliminating both copy and move operations.

- **Q2: Why does void f(int& x) reject f(10)?**  
  **A**: `10` is an rvalue (temporary literal). Non-const lvalue references can only bind to modifiable lvalues (variables with persistent memory addresses). To accept temporaries, use `const int&` or rvalue reference `int&&`.

- **Q3: What happens on the call stack during a function call?**  
  **A**: The CPU pushes the return instruction address, saves base pointer `RBP`, allocates stack space for local variables, and jumps to the function code. Upon return, the stack frame is popped.


---

## 9. Tags & Related Problems

- **Tags**: `C++ Basics`, `Functions`, `Memory`, `References`, `Easy`
- **Related problems to practice next**:
- **Learn Basic Recursion**: Call stack recursion frames.
- **C++ STL Complete Guide**: Passing containers.
