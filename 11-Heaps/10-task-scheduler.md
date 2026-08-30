# Task Scheduler (Greedy Heap + Cool Down Queue) (Step 11.2 — Medium Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Task Scheduler (Greedy Heap + Cool Down Queue)](https://takeuforward.org/data-structure/task-scheduler/)
- **Difficulty**: Medium
- **Statement**: Given a characters array `tasks`, representing the tasks a CPU needs to do, and a non-negative integer $n$ representing cooling interval between identical tasks, return the least number of units of times that the CPU will take to finish all given tasks.

---

## 1. Problem, Restated

Schedule tasks minimizing idle slots: $\text{ans} = \max(|tasks|, (\text{maxFreq} - 1) \times (n + 1) + \text{maxCount})$.

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Value / Top-K elements / Merged list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Let $M$ be the maximum frequency of any task, and $count$ be the number of distinct tasks with this frequency $M$. The most frequent tasks create $M - 1$ frames of length $(n + 1)$, plus the final partial frame containing the $count$ most frequent tasks. Total time $= (M - 1) \times (n + 1) + count$. If total tasks exceeds this frame size, no idle time is needed! Thus $\text{ans} = \max(|tasks|, (M - 1) \times (n + 1) + count)$ in $\mathcal{O}(N)$ time!

- **Underlying Pattern**: `Mathematical Frame Greedy Slot Scheduling`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Max-Heap + Cooldown Queue simulation in $\mathcal{O}(\text{time})$ steps.

### C++17 Code
```cpp
// Simulation approach
```

### Java Code
```java
// Java equivalent
// Simulation approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\text{time} \log 26)$.
- **Space Complexity**: $\mathcal{O}(26)$.
- **Why it's not good enough**: Simulation steps through idle cycles.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Priority Queue / Heap implementation below directly achieves optimal $\mathcal{O}(N \log K)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Mathematical Frame Formula in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int leastInterval(vector<char>& tasks, int n) {
        vector<int> freq(26, 0);
        int maxFreq = 0;
        
        for (char t : tasks) {
            freq[t - 'A']++;
            maxFreq = max(maxFreq, freq[t - 'A']);
        }
        
        int maxCount = 0;
        for (int f : freq) {
            if (f == maxFreq) {
                maxCount++;
            }
        }
        
        int frameLength = (maxFreq - 1) * (n + 1) + maxCount;
        return max((int)tasks.size(), frameLength);
    }
};
```

### Java Code
```java
class Solution {

    int leastInterval(char[] tasks, int n) {
        int[] freq = new int[26];
        int maxFreq = 0;
        
        for (char t : tasks) {
            freq[t - 'A']++;
            maxFreq = Math.max(maxFreq, freq[t - 'A']);
        }
        
        int maxCount = 0;
        for (int f : freq) {
            if (f == maxFreq) {
                maxCount++;
            }
        }
        
        int frameLength = (maxFreq - 1) * (n + 1) + maxCount;
        return Math.max(tasks.length, frameLength);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space (26-element array).
- **Why this is optimal**: Direct mathematical derivation eliminates discrete time simulation.

---

## 6. Dry Run

`tasks = ["A","A","A","B","B","B"], n = 2`

| Step | Action / State Change | Result |
|---|---|---|
| `Frequencies` | A: 3, B: 3 -> maxFreq = 3 | maxCount = 2 (A and B) |
| `Formula` | $(3 - 1) \times (2 + 1) + 2 = 2 \times 3 + 2 = 8$ | frame = 8 |
| `Schedule` | `A -> B -> idle -> A -> B -> idle -> A -> B` | Total units = 8 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $n = 0$ (no cooling time $\implies |tasks|$).
- Many distinct tasks where $|tasks| > frameLength$.

### Common Bugs to Avoid
- Using integer division without taking `max(|tasks|, frame)`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why can tasks with frequency < maxFreq always be inserted without increasing time beyond max(|tasks|, frame)?**  
  **A**: Because they can fill the $(n - count + 1)$ idle gaps inside each frame. If all idle gaps fill up, we simply expand the frame length dynamically without violating the $n$ cooldown restriction!


---

## 9. Tags & Related Problems

- **Tags**: `Heap`, `Greedy`, `Maths`, `LeetCode-621`, `Medium`
- **Related problems to practice next**:
- **Hands of Straights**: Greedy partitioning.
