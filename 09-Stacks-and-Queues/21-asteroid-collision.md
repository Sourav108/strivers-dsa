# Asteroid Collision (Step 9.3 — Monotonic Stack / Queue)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Asteroid Collision](https://takeuforward.org/data-structure/asteroid-collision/)
- **Difficulty**: Medium
- **Statement**: Given an array `asteroids` of integers representing asteroids in a row (positive moving right, negative moving left), find out the state of the asteroids after all collisions.

---

## 1. Problem, Restated

Simulate collisions: only right-moving positive asteroids (`>0`) and left-moving negative asteroids (`<0`) collide.

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Resulting vector of elements / integer answer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Collision occurs IF AND ONLY IF `stack.top() > 0` and current asteroid `ast < 0`. While collision condition holds: 1) If `abs(ast) > stack.top()`, pop stack asteroid (it explodes) and continue loop. 2) If `abs(ast) == stack.top()`, both explode (pop stack and mark `ast` exploded). 3) If `abs(ast) < stack.top()`, current asteroid explodes (break). If `ast` survives all collisions, push to stack.

- **Underlying Pattern**: `LIFO Collision Simulation Stack`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Repeated array deletions in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
// O(N^2) vector erasing
```

### Java Code
```java
// Java equivalent
// O(N^2) vector erasing
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Vector erasure shifting.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — monotonic data structure below directly achieves optimal $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Single Pass LIFO Collision Stack in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
#include <vector>
#include <cmath>
using namespace std;

class Solution {
public:
    vector<int> asteroidCollision(vector<int>& asteroids) {
        vector<int> st; // use vector as stack
        
        for (int ast : asteroids) {
            bool exploded = false;
            
            // Collision occurs only when top is moving right (>0) and current is moving left (<0)
            while (!st.empty() && st.back() > 0 && ast < 0) {
                if (abs(ast) > st.back()) {
                    st.pop_back(); // top explodes, current continues
                } else if (abs(ast) == st.back()) {
                    st.pop_back(); // both explode
                    exploded = true;
                    break;
                } else {
                    exploded = true; // current explodes
                    break;
                }
            }
            
            if (!exploded) {
                st.push_back(ast);
            }
        }
        
        return st;
    }
};
```

### Java Code
```java
class Solution {

    int[] asteroidCollision(int[] asteroids) {
        int[] st; // use vector as stack
        
        for (int ast : asteroids) {
            boolean exploded = false;
            
            // Collision occurs only when top is moving right (>0) and current is moving left (<0)
            while (!st.isEmpty() && st.peekLast() > 0 && ast < 0) {
                if (Math.abs(ast) > st.peekLast()) {
                    st.remove(); // top explodes, current continues
                } else if (Math.abs(ast) == st.peekLast()) {
                    st.remove(); // both explode
                    exploded = true;
                    break;
                } else {
                    exploded = true; // current explodes
                    break;
                }
            }
            
            if (exploded == null) {
                st.add(ast);
            }
        }
        
        return st;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time (each asteroid is pushed and popped at most once).
- **Space Complexity**: $\mathcal{O}(N)$ stack space.
- **Why this is optimal**: Using `vector` as stack directly returns results without reversing.

---

## 6. Dry Run

`asteroids = [5, 10, -5]`

| Step | Action / State Change | Result |
|---|---|---|
| `Push 5, 10` | st: `[5, 10]` | Moving right |
| `Incoming -5` | top=10 > 0, ast=-5 < 0 -> collision: `10 > |-5|` -> -5 explodes | st: `[5, 10]` |
| `Result` | `[5, 10]` | Surviving asteroids ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Negative asteroid on empty stack `[-2, -1, 1, 2]` (no collision $\implies$ pushed).
- Equal size collision `[8, -8]` $\implies$ `[]`.

### Common Bugs to Avoid
- Checking `st.top() < 0 && ast > 0` as collision (left-moving asteroid on left and right-moving asteroid on right move AWAY from each other, NEVER colliding!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why do negative asteroids at the start never collide?**  
  **A**: Because negative asteroids move left ($\leftarrow$). If they start at the left, they move away into $-\infty$, never colliding with asteroids behind them.


---

## 9. Tags & Related Problems

- **Tags**: `Stack`, `Simulation`, `LeetCode-735`, `Medium`
- **Related problems to practice next**:
- **Daily Temperatures**: Monotonic stack.
