# Detect a Loop in LinkedList (Floyd's Cycle Finding) (Step 6.3 — Medium Problems of LL)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Detect a Loop in LinkedList (Floyd's Cycle Finding)](https://takeuforward.org/data-structure/detect-a-cycle-in-a-linked-list/)
- **Difficulty**: Easy
- **Statement**: Given head, the head of a linked list, determine if the linked list has a cycle in it using Floyd's Cycle-Finding Algorithm.

---

## 1. Problem, Restated

Detect if pointer traversal enters an infinite loop without modifying node values.

- **Input**: Head of LinkedList / values to insert or delete.
- **Output**: Transformed LinkedList head / queried property.
- **Constraints**: Standard competitive programming limits ($0 \le N \le 10^5$).

---

## 2. Intuition & Pattern

If a cycle exists, a fast pointer moving 2 steps per iteration and a slow pointer moving 1 step per iteration will eventually meet inside the loop: relative speed is $2 - 1 = 1$ node per step, decreasing the gap by 1 on every iteration until collision `slow == fast`.

- **Underlying Pattern**: `Floyd's Cycle Detection (Tortoise and Hare)`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Hash Set of Node Addresses in $\mathcal{O}(N)$ time and space.

### C++17 Code
```cpp
#include <unordered_set>
using namespace std;
struct Node { int data; Node* next; };
bool hasCycleHash(Node* head) {
    unordered_set<Node*> visited;
    Node* curr = head;
    while (curr) {
        if (visited.count(curr)) return true;
        visited.insert(curr);
        curr = curr->next;
    }
    return false;
}
```

### Java Code
```java
static class Node { int data; Node  next; };
boolean hasCycleHash(Node  head) {
    unordered_set<Node > visited;
    Node  curr = head;
    while (curr) {
        if (visited.contains(curr)) return true;
        visited.add(curr);
        curr = curr.next;
    }
    return false;
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
Floyd's Tortoise and Hare Cycle Finding in $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

class Solution {
public:
    bool hasCycle(Node *head) {
        Node* slow = head;
        Node* fast = head;
        
        while (fast != nullptr && fast->next != nullptr) {
            slow = slow->next;         // 1 step
            fast = fast->next->next;   // 2 steps
            
            // Pointers collided inside cycle
            if (slow == fast) {
                return true;
            }
        }
        
        return false; // Reached nullptr -> No cycle
    }
};
```

### Java Code
```java
static class Node {
    int data;
    Node  next;
    public Node(int val) { /* initialized: data(val), next(null)  */  }
};

class Solution {

    boolean hasCycle(Node head) {
        Node  slow = head;
        Node  fast = head;
        
        while (fast != null && fast.next != null) {
            slow = slow.next;         // 1 step
            fast = fast.next.next;   // 2 steps
            
            // Pointers collided inside cycle
            if (slow == fast) {
                return true;
            }
        }
        
        return false; // Reached null . No cycle
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time — slow takes at most $N + K$ steps where $K \le N$ is cycle length.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Relative speed guarantees collision within loop length.

---

## 6. Dry Run

List with cycle: `1 -> 2 -> 3 -> 4 -> 2 (cycle back to 2)`

| Step | Action / State Change | Result |
|---|---|---|
| `Start` | slow = 1, fast = 1 | gap = loop distance |
| `Iter 1` | slow = 2, fast = 3 | gap decreases by 1 |
| `Iter 2` | slow = 3, fast = 2 (cycled) | gap decreases by 1 |
| `Iter 3` | slow = 4, fast = 4 -> slow == fast! | Cycle Detected -> Return TRUE ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Empty list or single node without cycle (returns `false`).
- Single node pointing to itself (returns `true`).

### Common Bugs to Avoid
- Accessing `fast->next->next` without verifying `fast->next != nullptr`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is collision guaranteed if a cycle exists?**  
  **A**: In discrete modulo arithmetic, the distance $d$ between fast and slow decreases by exactly $1$ on every iteration: $d_{new} = (d - 1) \pmod L$. Since $d$ decreases continuously, it must reach 0 (collision).


---

## 9. Tags & Related Problems

- **Tags**: `LinkedList`, `Cycle Detection`, `Fast Slow`, `LeetCode-141`, `Easy`
- **Related problems to practice next**:
- **Starting Point of Loop**: Cycle entry search.
- **Length of Loop**: Cycle length counting.
