# Implement Queue using Stacks (Amortized O(1)) (Step 9.1 — Learning)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Implement Queue using Stacks (Amortized O(1))](https://takeuforward.org/data-structure/implement-queue-using-stack/)
- **Difficulty**: Easy
- **Statement**: Implement a First-In-First-Out (FIFO) queue using two stacks `input` and `output` with amortized $\mathcal{O}(1)$ time complexity for `push`, `pop`, `peek`, and `empty`.

---

## 1. Problem, Restated

Transfer elements between `input` and `output` stacks lazily on demand.

- **Input**: Class methods / expression string.
- **Output**: Operation returns / transformed expression.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Maintain `input` stack (for push) and `output` stack (for pop/peek). 1) `push(x)`: `input.push(x)` ($\mathcal{O}(1)$). 2) `pop()` / `peek()`: if `output` is empty, pour ALL elements from `input` to `output` (this reverses LIFO into FIFO!). Then pop from `output`. Each element is moved at most twice, yielding **Amortized $\mathcal{O}(1)$** time per operation!

- **Underlying Pattern**: `Lazy Stack Transfer (Amortized $\mathcal{O}(1)$ Dual Stack)`.

---

## 3. Approach 1 — Naive / Basic Attempt

### Idea
Transferring all elements to auxiliary stack on EVERY push in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
// Push O(N) transfer approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ per push.
- **Space Complexity**: $\mathcal{O}(2N)$.
- **Why it's not good enough**: Eager transfer on every push.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic data structure implementation below directly provides optimal bounds.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
Lazy Dual-Stack Transfer with Amortized $\mathcal{O}(1)$ operations.

### C++17 Code
```cpp
#include <stack>
using namespace std;

class MyQueue {
private:
    stack<int> input;
    stack<int> output;
    
    void transferIfEmpty() {
        if (output.empty()) {
            while (!input.empty()) {
                output.push(input.top());
                input.pop();
            }
        }
    }

public:
    MyQueue() {}
    
    void push(int x) {
        input.push(x); // O(1)
    }
    
    int pop() {
        transferIfEmpty();
        int val = output.top();
        output.pop();
        return val; // Amortized O(1)
    }
    
    int peek() {
        transferIfEmpty();
        return output.top(); // Amortized O(1)
    }
    
    bool empty() {
        return input.empty() && output.empty();
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ for `push`, Amortized $\mathcal{O}(1)$ for `pop` and `peek`.
- **Space Complexity**: $\mathcal{O}(N)$ dual stack memory.
- **Why this is optimal**: Each element is pushed to `input` once and moved to `output` once ($2$ operations over element lifecycle).

---

## 6. Dry Run

`push(1), push(2), peek(), pop(), push(3), pop()`

| Step | Action / State Change | Result |
|---|---|---|
| `push(1), push(2)` | input: `[1, 2]`, output: `[]` | Pushed |
| `peek()` | input poured into output -> output: `[2, 1]` | returns 1 |
| `pop()` | pops 1 from output -> output: `[2]` | returns 1 |
| `push(3)` | input: `[3]`, output: `[2]` | Pushed |
| `pop()` | pops 2 from output (no transfer needed!) | returns 2 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `pop()` when both stacks empty.
- Multiple consecutive pushes and pops.

### Common Bugs to Avoid
- Transferring from `input` when `output` is NOT empty (scrambles FIFO order). Must only transfer when `output.empty()`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is pop() amortized O(1) even though a transfer takes O(N)?**  
  **A**: An $\mathcal{O}(N)$ transfer only occurs once after $N$ elements are pushed. Distributing the $N$ transfer cost over the $N$ individual pop operations gives $\frac{\mathcal{O}(N)}{N} = \mathcal{O}(1)$ amortized cost per pop (Accounting Method).


---

## 9. Tags & Related Problems

- **Tags**: `Queue`, `Stack`, `Amortized Analysis`, `LeetCode-232`, `Easy`
- **Related problems to practice next**:
- **Implement Stack using Queue**: Inverse structure.
