# Find the Starting Point of Loop in LinkedList (Step 6.3 — Medium Problems of LL)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Find the Starting Point of Loop in LinkedList](https://takeuforward.org/data-structure/find-the-starting-point-of-the-loop-in-linked-list/)
- **Difficulty**: Medium
- **Statement**: Given the head of a linked list, return the node where the cycle begins. If there is no cycle, return `null`.

---

## 1. Problem, Restated

Find the exact entry node of the cycle in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

- **Input**: Head of LinkedList / values to insert or delete.
- **Output**: Transformed LinkedList head / queried property.
- **Constraints**: Standard competitive programming limits ($0 \le N \le 10^5$).

---

## 2. Intuition & Pattern

Let $L_1$ be distance from head to cycle start, and $d$ be distance from cycle start to collision point. Slow traveled $L_1 + d$. Fast traveled $L_1 + d + k \cdot C$ (where $C$ is cycle length). Since $v_{fast} = 2 \cdot v_{slow}$: $2(L_1 + d) = L_1 + d + kC \implies L_1 = kC - d$. Thus, distance from head to cycle entry ($L_1$) is EXACTLY equal to distance from collision point to cycle entry! Reset `slow = head` and advance both `slow` and `fast` by 1 step; they will meet at the cycle entry node!

- **Underlying Pattern**: `Floyd's Cycle Entry Equidistance Theorem ($L_1 = C - d$)`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Hash set storing visited node addresses in $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <unordered_set>
using namespace std;
struct Node { int data; Node* next; };
Node* detectCycleHash(Node* head) {
    unordered_set<Node*> vis;
    Node* curr = head;
    while (curr) {
        if (vis.count(curr)) return curr;
        vis.insert(curr);
        curr = curr->next;
    }
    return nullptr;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ hash set space.
- **Why it's not good enough**: Uses extra memory.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard pointer manipulation below directly solves the problem.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Floyd's Reset Algorithm: collide, reset `slow = head`, advance both by 1 step.

### C++17 Code
```cpp
struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

class Solution {
public:
    Node *detectCycle(Node *head) {
        Node* slow = head;
        Node* fast = head;
        
        // Phase 1: Detect cycle and find collision point
        while (fast != nullptr && fast->next != nullptr) {
            slow = slow->next;
            fast = fast->next->next;
            
            if (slow == fast) {
                // Phase 2: Reset slow to head, advance both by 1 step
                slow = head;
                while (slow != fast) {
                    slow = slow->next;
                    fast = fast->next;
                }
                return slow; // Entry node of cycle!
            }
        }
        
        return nullptr; // No cycle
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Mathematical equidistance identity avoids hash table storage.

---

## 6. Dry Run

List: `3 -> 2 -> 0 -> -4 -> 2` ($L_1=1$, Cycle: `[2, 0, -4]`, $C=3$)

| Step | Action / State Change | Result |
|---|---|---|
| `Phase 1 Collide` | slow and fast collide at node `-4` | Collision point found |
| `Phase 2 Reset` | slow = head (3), fast stays at -4 | Both advance 1 step |
| `Step 1` | slow = 3->next (2), fast = -4->next (2) | slow == fast at Node [2]! |
| `Result` | Cycle Entry Node = [2] | Return Node [2] ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- No cycle (returns `nullptr`).
- Cycle starting at head ($L_1 = 0$).

### Common Bugs to Avoid
- Advancing `fast` by 2 steps in Phase 2 (must advance by 1 step!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does resetting slow to head find the starting point?**  
  **A**: Because distance from head to entry ($L_1$) equals distance from collision to entry ($kC - d$). Moving both at speed 1 guarantees they meet at the entry point after traveling distance $L_1$.


---

## 9. Tags & Related Problems

- **Tags**: `LinkedList`, `Cycle Detection`, `Floyd Algorithm`, `LeetCode-142`, `Medium`
- **Related problems to practice next**:
- **Detect a Loop**: Cycle presence check.
- **Length of Loop**: Cycle length.
