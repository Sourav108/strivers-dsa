# Implement Min Stack (O(1) time and space) (Step 9.1 — Learning)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Implement Min Stack (O(1) time and space)](https://takeuforward.org/data-structure/implement-min-stack-o2n-and-on-space-complexity/)
- **Difficulty**: Medium
- **Statement**: Design a stack that supports `push`, `pop`, `top`, and retrieving the minimum element `getMin` in $\mathcal{O}(1)$ time and $\mathcal{O}(1)$ extra auxiliary space.

---

## 1. Problem, Restated

Implement Min Stack encoding encoded modified values ($2x - \text{min}$) to track historical minimums without an auxiliary pair stack.

- **Input**: Class methods / expression string.
- **Output**: Operation returns / transformed expression.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

When a new element $x < \text{min}$ arrives: push the modified encoded value `2x - min` onto the stack (which is guaranteed to be strictly less than $x$!), then update `min = x`. When popping: if `top < min`, the previous minimum was `2*min - top`! This restores the previous minimum in $\mathcal{O}(1)$ time and strictly $\mathcal{O}(1)$ extra space!

- **Underlying Pattern**: `Mathematical Modified Value Encoding (`2x - min`)`.

---

## 3. Approach 1 — Naive / Basic Attempt

### Idea
Store pairs `(value, currentMin)` in stack in $\mathcal{O}(2N)$ memory.

### C++17 Code
```cpp
#include <stack>
using namespace std;
class MinStackPair {
    stack<pair<int, int>> st;
public:
    void push(int val) {
        int m = st.empty() ? val : min(val, st.top().second);
        st.push({val, m});
    }
    void pop() { st.pop(); }
    int top() { return st.top().first; }
    int getMin() { return st.top().second; }
};
```

### Java Code
```java
import java.util.*;

class MinStackPair {
    stack<pair<int, int>> st;

    void push(int val) {
        int m = st.isEmpty() ? val : Math.min(val, st.peek().second);
        st.push({val, m});
    }
    void pop() { st.pop(); }
    int top() { return st.peek().first; }
    int getMin() { return st.peek().second; }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ for all operations.
- **Space Complexity**: $\mathcal{O}(2N)$ memory.
- **Why it's not good enough**: Stores 2 integers per stack element.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic data structure implementation below directly provides optimal bounds.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
Encoded Modified Value Formula (`2x - min`) in $\mathcal{O}(N)$ total memory.

### C++17 Code
```cpp
#include <stack>
#include <climits>
using namespace std;

class MinStack {
private:
    stack<long long> st;
    long long mini;

public:
    MinStack() : mini(LLONG_MAX) {}
    
    void push(int val) {
        long long x = val;
        if (st.empty()) {
            mini = x;
            st.push(x);
        } else {
            if (x < mini) {
                // Push encoded flag value (2*x - mini) which is strictly < x
                st.push(2 * x - mini);
                mini = x; // update current minimum
            } else {
                st.push(x);
            }
        }
    }
    
    void pop() {
        if (st.empty()) return;
        long long topVal = st.top();
        st.pop();
        
        // If popped value is encoded flag, restore previous minimum
        if (topVal < mini) {
            mini = 2 * mini - topVal;
        }
    }
    
    int top() {
        if (st.empty()) return -1;
        long long topVal = st.top();
        if (topVal < mini) {
            return (int)mini; // the actual value pushed was mini
        }
        return (int)topVal;
    }
    
    int getMin() {
        return (int)mini;
    }
};
```

### Java Code
```java
import java.util.*;

class MinStack {

    stack<long> st;
    long mini;

    public MinStack() { /* initialized: mini(Long.MAX_VALUE)  */  }
    
    void push(int val) {
        long x = val;
        if (st.isEmpty()) {
            mini = x;
            st.push(x);
        } else {
            if (x < mini) {
                // Push encoded flag value (2x - mini) which is strictly < x
                st.push(2 * x - mini);
                mini = x; // update current minimum
            } else {
                st.push(x);
            }
        }
    }
    
    void pop() {
        if (st.isEmpty()) return;
        long topVal = st.peek();
        st.pop();
        
        // If popped value is encoded flag, restore previous minimum
        if (topVal < mini) {
            mini = 2 * mini - topVal;
        }
    }
    
    int top() {
        if (st.isEmpty()) return -1;
        long topVal = st.peek();
        if (topVal < mini) {
            return (int)mini; // the actual value pushed was mini
        }
        return (int)topVal;
    }
    
    int getMin() {
        return (int)mini;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ strict time for all 4 methods.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space (single scalar `mini` integer).
- **Why this is optimal**: Mathematical bijection encoding stores both value and previous minimum state in a single integer cell.

---

## 6. Dry Run

Push sequence: `push(5), push(3), push(7), push(2), pop()`

| Step | Action / State Change | Result |
|---|---|---|
| `push(5)` | st: `[5]`, mini = 5 | getMin() = 5 |
| `push(3)` | 3 < 5 -> push `2(3)-5 = 1`. st: `[5, 1]`, mini = 3 | getMin() = 3 |
| `push(7)` | 7 >= 3 -> push 7. st: `[5, 1, 7]`, mini = 3 | getMin() = 3 |
| `push(2)` | 2 < 3 -> push `2(2)-3 = 1`. st: `[5, 1, 7, 1]`, mini = 2 | getMin() = 2 |
| `pop()` | topVal 1 < mini(2) -> restore mini = `2(2)-1 = 3` | mini restored to 3 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Values equal to `INT_MIN` or `INT_MAX` (using `long long` prevents arithmetic overflow in $2x - \text{mini}$).

### Common Bugs to Avoid
- Integer overflow on `2 * x` without 64-bit `long long` cast.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is the encoded value 2*x - mini guaranteed to be strictly less than x?**  
  **A**: Since $x < \text{mini} \implies x - \text{mini} < 0$. Adding $x$ to both sides yields $2x - \text{mini} < x$. Thus, an encoded value is ALWAYS strictly less than the new minimum $x$, serving as an unambiguous flag!


---

## 9. Tags & Related Problems

- **Tags**: `Stack`, `Design`, `Maths`, `LeetCode-155`, `Medium`
- **Related problems to practice next**:
- **Check Balanced Parentheses**: Stack basics.
