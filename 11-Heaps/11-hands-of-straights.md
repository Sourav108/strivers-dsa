# Hands of Straights (Consecutive Group Partitioning) (Step 11.2 — Medium Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Hands of Straights (Consecutive Group Partitioning)](https://takeuforward.org/data-structure/hand-of-straights/)
- **Difficulty**: Medium
- **Statement**: Alice has some number of cards and wants to rearrange the cards into groups so that each group is of size `groupSize`, and consists of `groupSize` consecutive cards. Return `true` if she can.

---

## 1. Problem, Restated

Partition array into consecutive runs of length `groupSize` using Min-Heap / Ordered Map.

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Value / Top-K elements / Merged list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) If $N \% \text{groupSize} \ne 0$, return `false`. 2) Count frequencies in an ordered map (or hash map + min-heap of unique keys). 3) Always start a group with the smallest available card $x$. 4) For $i = 0$ to $\text{groupSize}-1$: check if $(x + i)$ exists with sufficient count. If not, return `false`; else decrement count.

- **Underlying Pattern**: `Greedy Consecutive Group Matching with Ordered Map / Min-Heap`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Repeated sorting and element erasing in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
// O(N^2) erase search
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Vector erase shifting.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Priority Queue / Heap implementation below directly achieves optimal $\mathcal{O}(N \log K)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Ordered Map Greedy Group Matching in $\mathcal{O}(N \log K)$ time.

### C++17 Code
```cpp
#include <vector>
#include <map>
using namespace std;

class Solution {
public:
    bool isNStraightHand(vector<int>& hand, int groupSize) {
        int n = hand.size();
        if (n % groupSize != 0) return false;
        
        map<int, int> countMap;
        for (int card : hand) {
            countMap[card]++;
        }
        
        for (auto& [card, count] : countMap) {
            if (count > 0) {
                int needed = count;
                for (int i = 0; i < groupSize; i++) {
                    if (countMap[card + i] < needed) {
                        return false; // consecutive run broken
                    }
                    countMap[card + i] -= needed;
                }
            }
        }
        
        return true;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log U)$ time where $U$ is unique card count.
- **Space Complexity**: $\mathcal{O}(U)$ map space.
- **Why this is optimal**: Smallest card must always be the start of a consecutive group.

---

## 6. Dry Run

`hand = [1, 2, 3, 6, 2, 3, 4, 7, 8], groupSize = 3`

| Step | Action / State Change | Result |
|---|---|---|
| `Group 1` | Start card 1: deduct 1, 2, 3 | Remaining `{2:1, 3:1, 4:1, 6:1, 7:1, 8:1}` |
| `Group 2` | Start card 2: deduct 2, 3, 4 | Remaining `{6:1, 7:1, 8:1}` |
| `Group 3` | Start card 6: deduct 6, 7, 8 | All counts 0 -> Return TRUE ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N \% groupSize \ne 0$ (instant `false`).
- $groupSize = 1$ (always `true`).

### Common Bugs to Avoid
- Subtracting 1 instead of `needed = count` on batch card deductions.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Can this be solved in O(N) using an unordered_map?**  
  **A**: Yes! Loop through cards: if `card - 1` is NOT in map (meaning `card` is the start of a sequence), greedily deduct `groupSize` consecutive cards from `card` forward in $\mathcal{O}(N)$ linear time.


---

## 9. Tags & Related Problems

- **Tags**: `Heap`, `Ordered Map`, `Greedy`, `LeetCode-846`, `Medium`
- **Related problems to practice next**:
- **Task Scheduler**: Greedy scheduling.
