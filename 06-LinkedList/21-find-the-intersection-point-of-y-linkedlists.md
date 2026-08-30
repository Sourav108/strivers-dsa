# Find the Intersection Point of Y LinkedLists (Step 6.3 — Medium Problems of LL)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Find the Intersection Point of Y LinkedLists](https://takeuforward.org/data-structure/find-intersection-of-two-linked-lists/)
- **Difficulty**: Medium
- **Statement**: Given the heads of two singly linked lists `headA` and `headB`, return the node at which the two lists intersect. If the two linked lists have no intersection at all, return `null`.

---

## 1. Problem, Restated

Find the common shared node in $\mathcal{O}(N + M)$ time and $\mathcal{O}(1)$ space.

- **Input**: Head pointer of LinkedList(s) / parameters.
- **Output**: Modified LinkedList head / queried node.
- **Constraints**: Standard competitive programming limits ($0 \le N \le 10^5$).

---

## 2. Intuition & Pattern

Initialize `pA = headA` and `pB = headB`. When `pA` reaches `nullptr`, redirect it to `headB`. When `pB` reaches `nullptr`, redirect it to `headA`. Both pointers will travel exactly $(L_A + L_B)$ distance, guaranteeing they either collide at the intersection node or reach `nullptr` simultaneously!

- **Underlying Pattern**: `Equi-Travel Pointer Switching ($a + c + b = b + c + a$)`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Hash Set storing all node addresses of List A in $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <unordered_set>
using namespace std;
struct Node { int data; Node* next; };
Node* getIntersectionNodeHash(Node *headA, Node *headB) {
    unordered_set<Node*> st;
    while (headA) { st.insert(headA); headA = headA->next; }
    while (headB) { if (st.count(headB)) return headB; headB = headB->next; }
    return nullptr;
}
```

### Java Code
```java
static class Node { int data; Node  next; };
Node  getIntersectionNodeHash(Node headA, Node headB) {
    unordered_set<Node > st;
    while (headA) { st.add(headA); headA = headA.next; }
    while (headB) { if (st.contains(headB)) return headB; headB = headB.next; }
    return null;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N + M)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ hash space.
- **Why it's not good enough**: Uses extra memory.

---

## 4. Approach 2 — Better

### Idea
Length Difference: count lengths, advance longer list by difference, then traverse together.

### C++17 Code
```cpp
struct Node { int data; Node* next; };
Node* getIntersectionNodeDiff(Node *headA, Node *headB) {
    int l1 = 0, l2 = 0;
    Node *c1 = headA, *c2 = headB;
    while (c1) { l1++; c1 = c1->next; }
    while (c2) { l2++; c2 = c2->next; }
    c1 = headA; c2 = headB;
    if (l1 > l2) for (int i = 0; i < l1 - l2; i++) c1 = c1->next;
    else for (int i = 0; i < l2 - l1; i++) c2 = c2->next;
    while (c1 && c2) {
        if (c1 == c2) return c1;
        c1 = c1->next; c2 = c2->next;
    }
    return nullptr;
}
```

### Java Code
```java
static class Node { int data; Node  next; };
Node  getIntersectionNodeDiff(Node headA, Node headB) {
    int l1 = 0, l2 = 0;
    Node c1 = headA, c2 = headB;
    while (c1) { l1++; c1 = c1.next; }
    while (c2) { l2++; c2 = c2.next; }
    c1 = headA; c2 = headB;
    if (l1 > l2) for (int i = 0; i < l1 - l2; i++) c1 = c1.next;
    else for (int i = 0; i < l2 - l1; i++) c2 = c2.next;
    while (c1 && c2) {
        if (c1 == c2) return c1;
        c1 = c1.next; c2 = c2.next;
    }
    return null;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N + M)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ space.
- **Why it's still not optimal**: Requires multiple passes for length calculation.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
2-Pointer Boundary Switching in $\mathcal{O}(N + M)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

class Solution {
public:
    Node *getIntersectionNode(Node *headA, Node *headB) {
        if (!headA || !headB) return nullptr;
        
        Node* pA = headA;
        Node* pB = headB;
        
        // Loop terminates when pA == pB (either at intersection node or both nullptr)
        while (pA != pB) {
            pA = (pA == nullptr) ? headB : pA->next;
            pB = (pB == nullptr) ? headA : pB->next;
        }
        
        return pA;
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

    Node getIntersectionNode(Node headA, Node headB) {
        if (!headA || !headB) return null;
        
        Node  pA = headA;
        Node  pB = headB;
        
        // Loop terminates when pA == pB (either at intersection node or both null)
        while (pA != pB) {
            pA = (pA == null) ? headB : pA.next;
            pB = (pB == null) ? headA : pB.next;
        }
        
        return pA;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N + M)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Equalizes path lengths without counting lengths upfront.

---

## 6. Dry Run

List A: `[4, 1, 8, 4, 5]` ($L_A=5$), List B: `[5, 6, 1, 8, 4, 5]` ($L_B=6$), Intersect at [8]

| Step | Action / State Change | Result |
|---|---|---|
| `Pass 1` | pA reaches end of A (5 steps), redirects to headB | pB reaches end of B (6 steps), redirects to headA |
| `Pass 2` | Both pointers aligned at offset | pA and pB meet at Node [8]! |
| `Result` | pA == pB == [8] | Return Node [8] ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- No intersection (both reach `nullptr` simultaneously after 2 passes).
- Intersection at head.

### Common Bugs to Avoid
- Switching pointer to head when `pA->next == nullptr` instead of `pA == nullptr` (skips null comparison on no-intersection).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is pA == pB guaranteed to terminate when no intersection exists?**  
  **A**: If no intersection, both pointers will travel $(L_A + L_B)$ steps and become `nullptr` simultaneously, satisfying `pA == pB == nullptr` and exiting.


---

## 9. Tags & Related Problems

- **Tags**: `LinkedList`, `Two Pointers`, `LeetCode-160`, `Medium`
- **Related problems to practice next**:
- **Detect a Loop**: Cycle intersection analogy.
