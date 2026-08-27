# Rotate a LinkedList by K places (Step 6.5 — Hard Problems of LL)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Rotate a LinkedList by K places](https://takeuforward.org/data-structure/rotate-a-linked-list/)
- **Difficulty**: Medium
- **Statement**: Given the head of a linked list, rotate the list to the right by $k$ places (e.g. `[1,2,3,4,5]` rotated by $k=2 \implies `[4,5,1,2,3]`$).

---

## 1. Problem, Restated

Make list circular by connecting tail to head, then break the ring at position $(L - k \% L)$.

- **Input**: Head pointer of LinkedList(s) / parameters.
- **Output**: Modified LinkedList head / queried node.
- **Constraints**: Standard competitive programming limits ($0 \le N \le 10^5$).

---

## 2. Intuition & Pattern

1) Find length $L$ and tail node. 2) Connect `tail->next = head` to form a circular ring. 3) Effective rotations: $k = k \% L$. If $k == 0$, break ring and return `head`. 4) Traverse to node $(L - k)$ from head: `newTail = node(L - k)`. 5) `newHead = newTail->next; newTail->next = nullptr; return newHead;`.

- **Underlying Pattern**: `Circular Ring Connection & Cut at $(L - k \% L)$`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Rotate 1 step at a time $K$ times in $\mathcal{O}(K \times N)$ time.

### C++17 Code
```cpp
// O(K * N) repetitive shift
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(K \times N)$ time — TLE for $K = 2 \times 10^9$.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Fails when $K$ is large.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard pointer manipulation below directly solves the problem.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Modulo arithmetic on circular ring in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

class Solution {
public:
    Node* rotateRight(Node* head, int k) {
        if (!head || !head->next || k == 0) return head;
        
        // 1. Calculate length and find tail
        int length = 1;
        Node* tail = head;
        while (tail->next != nullptr) {
            length++;
            tail = tail->next;
        }
        
        // 2. Modulo reduction
        k = k % length;
        if (k == 0) return head;
        
        // 3. Connect tail to head (Circular Ring)
        tail->next = head;
        
        // 4. Find new tail at (length - k)
        int stepsToNewTail = length - k;
        Node* newTail = head;
        for (int i = 1; i < stepsToNewTail; i++) {
            newTail = newTail->next;
        }
        
        // 5. Break the ring
        Node* newHead = newTail->next;
        newTail->next = nullptr;
        
        return newHead;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N + (N - k)) = \mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Modulo reduction eliminates redundant full-circle rotations.

---

## 6. Dry Run

`[1, 2, 3, 4, 5]`, $k = 2$ ($L = 5$)

| Step | Action / State Change | Result |
|---|---|---|
| `Make circular` | [5]->next = [1] | Ring created |
| `stepsToNewTail` | $5 - 2 = 3$ | newTail = Node [3] |
| `Break link` | newHead = [4], [3]->next = NULL | `[4, 5, 1, 2, 3]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $k = 0$ or $k \% L == 0$ (returns original head).
- Single node list.

### Common Bugs to Avoid
- Rotating without modulo `k % length` (times out on $k = 2000000000$).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does (length - k) give the new tail?**  
  **A**: Rotating right by $k$ places moves the last $k$ nodes to the front. The remaining first $(L - k)$ nodes become the end of the new list, so node $(L - k)$ is the new tail.


---

## 9. Tags & Related Problems

- **Tags**: `LinkedList`, `Circular`, `LeetCode-61`, `Medium`
- **Related problems to practice next**:
- **Rotate Array**: Array rotation.
