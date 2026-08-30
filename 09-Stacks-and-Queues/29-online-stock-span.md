# Online Stock Span (Step 9.4 — Implementation Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Online Stock Span](https://takeuforward.org/data-structure/online-stock-span/)
- **Difficulty**: Medium
- **Statement**: Design an algorithm that collects daily price quotes for some stock and returns the span of that stock's price for the current day (maximum number of consecutive days ending today where price $\le$ today's price).

---

## 1. Problem, Restated

Compute distance to Previous Greater Element (PGE) for dynamic streaming prices using a monotonic stack.

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Resulting vector of elements / integer answer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Maintain stack storing pairs `(price, span)`. When a new `price` arrives: initialize `span = 1`. While `!st.empty() && st.top().first <= price`, add `span += st.top().second` and pop `st.pop()`. Push `{price, span}` and return `span`. Each price is pushed and popped at most once $\implies$ **Amortized $\mathcal{O}(1)$** time!

- **Underlying Pattern**: `Monotonic Decreasing Stack with Running Span Accumulation`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Store all prices in vector and loop backwards on every call in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
#include <vector>
using namespace std;
class StockSpannerBrute {
    vector<int> prices;
public:
    int next(int price) {
        prices.push_back(price);
        int span = 0;
        for (int i = (int)prices.size() - 1; i >= 0 && prices[i] <= price; i--) span++;
        return span;
    }
};
```

### Java Code
```java
import java.util.*;

class StockSpannerBrute {
    List<Integer> prices = new ArrayList<>();

    int next(int price) {
        prices.add(price);
        int span = 0;
        for (int i = prices.length - 1; i >= 0 && prices[i] <= price; i--) span++;
        return span;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ per call (worst case $\mathcal{O}(N^2)$ total).
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Backwards scanning repeats work.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — monotonic data structure below directly achieves optimal $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Monotonic Stack Span Accumulator in Amortized $\mathcal{O}(1)$ time.

### C++17 Code
```cpp
#include <stack>
#include <utility>
using namespace std;

class StockSpanner {
private:
    // Stores pair: {price, accumulated_span}
    stack<pair<int, int>> st;

public:
    StockSpanner() {}
    
    int next(int price) {
        int span = 1;
        
        while (!st.empty() && st.top().first <= price) {
            span += st.top().second;
            st.pop();
        }
        
        st.push({price, span});
        return span;
    }
};
```

### Java Code
```java
class StockSpanner {

    // Stores pair: {price, accumulated_span}
    stack<pair<int, int>> st;

    StockSpanner() {}
    
    int next(int price) {
        int span = 1;
        
        while (!st.isEmpty() && st.peek().first <= price) {
            span += st.peek().second;
            st.pop();
        }
        
        st.push({price, span});
        return span;
    }
};
```

### Complexity Derivation
- **Time Complexity**: Amortized $\mathcal{O}(1)$ time per `next()` call.
- **Space Complexity**: $\mathcal{O}(N)$ stack space.
- **Why this is optimal**: Accumulating spans compresses popped sub-sequences into a single scalar.

---

## 6. Dry Run

Stream: `next(100), next(80), next(60), next(70), next(60), next(75), next(85)`

| Step | Action / State Change | Result |
|---|---|---|
| `100` | st: `[{100, 1}]` | span = 1 |
| `80, 60` | st: `[{100,1}, {80,1}, {60,1}]` | span = 1, 1 |
| `70` | pops {60,1} -> span = 1+1=2, st: `[{100,1}, {80,1}, {70,2}]` | span = 2 |
| `60` | st: `[{100,1}, {80,1}, {70,2}, {60,1}]` | span = 1 |
| `75` | pops {60,1}, {70,2} -> span = 1+1+2=4 | span = 4 |
| `85` | pops {75,4}, {80,1} -> span = 1+4+1=6 | span = 6 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Strictly increasing price stream (spans: 1, 2, 3, 4...).
- Strictly decreasing prices (spans: 1, 1, 1...).

### Common Bugs to Avoid
- Using `<` instead of `<=` (consecutive equal prices must be accumulated into span).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is span accumulation faster than storing indices?**  
  **A**: Storing the accumulated span directly in the stack compresses entire descending intervals into single nodes, avoiding index conversions.


---

## 9. Tags & Related Problems

- **Tags**: `Stack`, `Monotonic Stack`, `Design`, `LeetCode-901`, `Medium`
- **Related problems to practice next**:
- **Next Greater Element I**: Monotonic lookup.
