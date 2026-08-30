# Find the Length of Loop in LinkedList (Step 6.3 — Medium Problems of LL)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Find the Length of Loop in LinkedList](https://takeuforward.org/data-structure/find-length-of-loop-in-linked-list/)
- **Difficulty**: Easy
- **Statement**: Given the head of a linked list, find and return the length of the cycle (number of nodes in the loop). If no cycle exists, return 0.

---

## 1. Problem, Restated

Count number of unique nodes in the cycle loop.

- **Input**: Head of LinkedList / values to insert or delete.
- **Output**: Transformed LinkedList head / queried property.
- **Constraints**: Standard competitive programming limits ($0 \le N \le 10^5$).

---

## 2. Intuition & Pattern

1) Use Floyd's cycle detection (`slow`, `fast`). 2) When `slow == fast`, freeze `fast` and traverse `slow = slow->next` counting steps until `slow == fast` again. Return `length`.

- **Underlying Pattern**: `Cycle Collision Traversal Counting`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Hash map recording visit timestamps in $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <unordered_map>
using namespace std;
struct Node { int data; Node* next; };
int countNodesinLoopHash(Node* head) {
    unordered_map<Node*, int> timer;
    Node* curr = head;
    int t = 1;
    while (curr) {
        if (timer.count(curr)) return t - timer[curr];
        timer[curr] = t++;
        curr = curr->next;
    }
    return 0;
}
```

### Java Code
```java
static class Node { int data; Node  next; };
int countNodesinLoopHash(Node  head) {
    unordered_map<Node , int> timer;
    Node  curr = head;
    int t = 1;
    while (curr) {
        if (timer.contains(curr)) return t - timer[curr];
        timer[curr] = t++;
        curr = curr.next;
    }
    return 0;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ hash map space.
- **Why it's not good enough**: Hash map overhead.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard pointer manipulation below directly solves the problem.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Floyd's collision + single loop traversal count in $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

int countNodesinLoop(Node *head) {
    Node* slow = head;
    Node* fast = head;
    
    while (fast != nullptr && fast->next != nullptr) {
        slow = slow->next;
        fast = fast->next->next;
        
        if (slow == fast) {
            // Count loop length
            int length = 1;
            Node* temp = slow->next;
            while (temp != slow) {
                length++;
                temp = temp->next;
            }
            return length;
        }
    }
    
    return 0; // No loop
}
```

### Java Code
```java
static class Node {
    int data;
    Node  next;
    public Node(int val) { /* initialized: data(val), next(null)  */  }
};

int countNodesinLoop(Node head) {
    Node  slow = head;
    Node  fast = head;
    
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
        
        if (slow == fast) {
            // Count loop length
            int length = 1;
            Node  temp = slow.next;
            while (temp != slow) {
                length++;
                temp = temp.next;
            }
            return length;
        }
    }
    
    return 0; // No loop
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Traversing the cycle once after collision counts exact nodes in $\mathcal{O}(C)$.

---

## 6. Dry Run

Cycle of length 3: `1 -> 2 -> 3 -> 4 -> 2`

| Step | Action / State Change | Result |
|---|---|---|
| `Collision` | slow and fast meet at node 4 | temp = 4->next = 2, length = 1 |
| `temp = 2` | temp = 2->next = 3, length = 2 | temp != 4 |
| `temp = 3` | temp = 3->next = 4, length = 3 | temp != 4 |
| `temp = 4` | temp == slow -> Loop complete | Return length = 3 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- No cycle (returns 0).
- Cycle of length 1 (node pointing to itself -> returns 1).

### Common Bugs to Avoid
- Setting `length = 0` when `temp = slow` (must start `length = 1, temp = slow->next`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does starting from the collision point give the correct length?**  
  **A**: Because the collision point is guaranteed to lie inside the cycle. Traversing from any node inside a cycle until returning to it visits every cycle node exactly once.


---

## 9. Tags & Related Problems

- **Tags**: `LinkedList`, `Cycle Detection`, `Easy`
- **Related problems to practice next**:
- **Starting Point of Loop**: Cycle entry.
- **Detect a Loop**: Cycle check.
