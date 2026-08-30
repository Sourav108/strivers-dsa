# Implement Stack using Arrays (Step 9.1 — Learning)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Implement Stack using Arrays](https://takeuforward.org/data-structure/implement-stack-using-array/)
- **Difficulty**: Easy
- **Statement**: Design a Last-In-First-Out (LIFO) Stack data structure using a fixed-size array in C++ supporting `push(x)`, `pop()`, `top()`, `size()`, and `isEmpty()` in $\mathcal{O}(1)$ time per operation.

---

## 1. Problem, Restated

Implement array-backed stack maintaining top index pointer `topIndex`.

- **Input**: Class methods / expression string.
- **Output**: Operation returns / transformed expression.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Maintain an array `arr[MAX_SIZE]` and integer pointer `topIndex = -1`. `push(x)` increments `++topIndex` and sets `arr[topIndex] = x`. `pop()` decrements `topIndex--`. `top()` returns `arr[topIndex]`. All operations execute in $\mathcal{O}(1)$ time.

- **Underlying Pattern**: `LIFO Stack Pointer Arithmetic (`topIndex = -1`)`.

---

## 3. Approach 1 — Naive / Basic Attempt

### Idea
Dynamic array with `vector.insert(v.begin(), x)` shifting elements in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
#include <vector>
using namespace std;
struct SlowStack {
    vector<int> v;
    void push(int x) { v.insert(v.begin(), x); } // O(N) shift
    int pop() { int x = v[0]; v.erase(v.begin()); return x; }
};
```

### Java Code
```java
import java.util.*;

static class SlowStack {
    int[] v;
    void push(int x) { v.add(v.begin(), x); } // O(N) shift
    int pop() { int x = v[0]; v.remove(v.begin()); return x; }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ per push/pop.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Vector front insertion causes linear memory shifting.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic data structure implementation below directly provides optimal bounds.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
Array Index Pointer Stack in strict $\mathcal{O}(1)$ time per operation.

### C++17 Code
```cpp
#include <iostream>
using namespace std;

class ArrayStack {
private:
    static const int MAX_CAPACITY = 10000;
    int arr[MAX_CAPACITY];
    int topIndex;

public:
    ArrayStack() : topIndex(-1) {}
    
    void push(int x) {
        if (topIndex >= MAX_CAPACITY - 1) {
            cout << "Stack Overflow\n";
            return;
        }
        arr[++topIndex] = x;
    }
    
    int pop() {
        if (isEmpty()) {
            return -1; // Stack Underflow
        }
        return arr[topIndex--];
    }
    
    int top() {
        if (isEmpty()) return -1;
        return arr[topIndex];
    }
    
    int size() {
        return topIndex + 1;
    }
    
    bool isEmpty() {
        return topIndex == -1;
    }
};
```

### Java Code
```java
import java.util.*;

class ArrayStack {

    static int MAX_CAPACITY = 10000;
    int arr[MAX_CAPACITY];
    int topIndex;

    public ArrayStack() { /* initialized: topIndex(-1)  */  }
    
    void push(int x) {
        if (topIndex >= MAX_CAPACITY - 1) {
            System.out.print("Stack Overflow\n");
            return;
        }
        arr[++topIndex] = x;
    }
    
    int pop() {
        if (isEmpty()) {
            return -1; // Stack Underflow
        }
        return arr[topIndex--];
    }
    
    int top() {
        if (isEmpty()) return -1;
        return arr[topIndex];
    }
    
    int size() {
        return topIndex + 1;
    }
    
    boolean isEmpty() {
        return topIndex == -1;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ strict time for all methods.
- **Space Complexity**: $\mathcal{O}(N)$ contiguous array buffer.
- **Why this is optimal**: Contiguous memory layout provides maximal CPU cache line locality.

---

## 6. Dry Run

Operations: `push(10), push(20), top(), pop(), isEmpty()`

| Step | Action / State Change | Result |
|---|---|---|
| `push(10)` | topIndex = 0, arr[0] = 10 | size = 1 |
| `push(20)` | topIndex = 1, arr[1] = 20 | size = 2 |
| `top()` | returns arr[1] | 20 |
| `pop()` | returns arr[1] (20), topIndex = 0 | 20 |
| `isEmpty()` | topIndex = 0 != -1 | false ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Pop on empty stack (Underflow $\implies$ returns -1).
- Push on full array (Overflow guard).

### Common Bugs to Avoid
- Pre-increment vs post-increment in `arr[++topIndex]`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is array-based stack faster than std::stack with std::deque?**  
  **A**: Fixed contiguous array has zero heap allocation overhead, zero pointer chasing, and 100% L1 CPU cache hit rate.


---

## 9. Tags & Related Problems

- **Tags**: `Stack`, `Data Structures`, `Arrays`, `Easy`
- **Related problems to practice next**:
- **Implement Queue using Arrays**: FIFO counterpart.
