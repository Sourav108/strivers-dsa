# The Celebrity Problem (Stack & Two Pointer Elimination) (Step 9.4 — Implementation Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [The Celebrity Problem (Stack & Two Pointer Elimination)](https://takeuforward.org/data-structure/the-celebrity-problem/)
- **Difficulty**: Medium
- **Statement**: A celebrity is a person who is known to everyone at a party, but does not know anyone. Given an $n \times n$ matrix `M` where `M[i][j] = 1` means person $i$ knows person $j$, find the celebrity (or return -1).

---

## 1. Problem, Restated

Find candidate in $\mathcal{O}(N)$ time using 2-pointer elimination, then verify celebrity condition.

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Resulting vector of elements / integer answer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Compare two people $A$ and $B$: 1) If $A$ knows $B$ (`M[A][B] == 1`), $A$ CANNOT be the celebrity (celebrities know no one) $\implies$ eliminate $A$. 2) If $A$ does not know $B$ (`M[A][B] == 0`), $B$ CANNOT be the celebrity (everyone knows the celebrity) $\implies$ eliminate $B$. Each comparison eliminates exactly one person, reducing $N$ candidates to 1 in $N-1$ steps! Finally, verify the candidate in $\mathcal{O}(N)$ time.

- **Underlying Pattern**: `Two-Pointer / Stack Candidate Elimination Invariant`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Compute indegrees and outdegrees of all $N$ people in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
// O(N^2) indegree outdegree check
```

### Java Code
```java
// Java equivalent
// O(N^2) indegree outdegree check
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Scans full matrix.

---

## 4. Approach 2 — Better

### Idea
Stack-based candidate elimination in O(N) time and O(N) space.

### C++17 Code
```cpp
#include <vector>
#include <stack>
using namespace std;
int celebrityStack(vector<vector<int>>& mat) {
    int n = mat.size();
    stack<int> st;
    for (int i = 0; i < n; i++) st.push(i);
    while (st.size() > 1) {
        int a = st.top(); st.pop();
        int b = st.top(); st.pop();
        if (mat[a][b] == 1) st.push(b);
        else st.push(a);
    }
    int candidate = st.top();
    for (int i = 0; i < n; i++) {
        if (i != candidate && (mat[candidate][i] == 1 || mat[i][candidate] == 0)) return -1;
    }
    return candidate;
}
```

### Java Code
```java
import java.util.*;

class Solution {
    int celebrityStack(int[][] mat) {
        int n = mat.length;
        Stack<Integer> st = new Stack<>();
        for (int i = 0; i < n; i++) st.push(i);
        while (st.length > 1) {
            int a = st.peek(); st.pop();
            int b = st.peek(); st.pop();
            if (mat[a][b] == 1) st.push(b);
            else st.push(a);
        }
        int candidate = st.peek();
        for (int i = 0; i < n; i++) {
            if (i != candidate && (mat[candidate][i] == 1 || mat[i][candidate] == 0)) return -1;
        }
        return candidate;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ stack space.
- **Why it's still not optimal**: Uses auxiliary stack memory.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Two Pointers Elimination in $\mathcal{O}(N)$ time and strict $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
public:
    int celebrity(vector<vector<int>>& mat) {
        int n = mat.size();
        int top = 0;
        int down = n - 1;
        
        // Step 1: Eliminate candidates using two pointers
        while (top < down) {
            if (mat[top][down] == 1) {
                // top knows down -> top cannot be celebrity
                top++;
            } else {
                // top does not know down -> down cannot be celebrity
                down--;
            }
        }
        
        int candidate = top;
        
        // Step 2: Verification pass in O(N)
        for (int i = 0; i < n; i++) {
            if (i == candidate) continue;
            
            // Candidate must know no one (mat[cand][i] == 0)
            // Everyone must know candidate (mat[i][cand] == 1)
            if (mat[candidate][i] == 1 || mat[i][candidate] == 0) {
                return -1; // verification failed
            }
        }
        
        return candidate;
    }
};
```

### Java Code
```java
class Solution {

    int celebrity(int[][] mat) {
        int n = mat.length;
        int top = 0;
        int down = n - 1;
        
        // Step 1: Eliminate candidates using two pointers
        while (top < down) {
            if (mat[top][down] == 1) {
                // top knows down . top cannot be celebrity
                top++;
            } else {
                // top does not know down . down cannot be celebrity
                down--;
            }
        }
        
        int candidate = top;
        
        // Step 2: Verification pass in O(N)
        for (int i = 0; i < n; i++) {
            if (i == candidate) continue;
            
            // Candidate must know no one (mat[cand][i] == 0)
            // Everyone must know candidate (mat[i][cand] == 1)
            if (mat[candidate][i] == 1 || mat[i][candidate] == 0) {
                return -1; // verification failed
            }
        }
        
        return candidate;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time ($N-1$ comparisons $+ 2N$ verifications).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Eliminates one candidate per comparison with zero auxiliary data structures.

---

## 6. Dry Run

`mat` ($3 \times 3$): `[[0, 1, 0], [0, 0, 0], [0, 1, 0]]` (Person 1 is celebrity)

| Step | Action / State Change | Result |
|---|---|---|
| `top=0, down=2` | mat[0][2] = 0 -> down becomes 1 | down = 1 |
| `top=0, down=1` | mat[0][1] = 1 -> top becomes 1 | top = down = 1 |
| `Candidate` | Candidate = 1 | Verification |
| `Verify Row 1` | mat[1][0]=0, mat[1][2]=0 (knows no one) | Valid |
| `Verify Col 1` | mat[0][1]=1, mat[2][1]=1 (everyone knows 1) | Celebrity = 1 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- No celebrity exists (verification returns -1).
- Matrix $1 \times 1$ (returns 0).

### Common Bugs to Avoid
- Skipping verification pass (candidate from elimination step is only a POTENTIAL celebrity; verification is mandatory!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is the verification pass strictly required?**  
  **A**: Because elimination only guarantees that NO OTHER person can be a celebrity. It does NOT guarantee that the remaining candidate actually satisfies the definition (e.g. if everyone knows no one, candidate 0 will survive elimination but fail verification).


---

## 9. Tags & Related Problems

- **Tags**: `Stack`, `Two Pointers`, `Arrays`, `Medium`
- **Related problems to practice next**:
- **Find the Town Judge**: Graph outdegree.
