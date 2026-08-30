# Reverse a LinkedList (Iterative & Recursive) (Step 6.3 — Medium Problems of LL)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Reverse a LinkedList (Iterative & Recursive)](https://takeuforward.org/data-structure/reverse-a-linked-list/)
- **Difficulty**: Easy
- **Statement**: Given the head of a singly linked list, reverse the list, and return the reversed list head using both iterative (3-pointer) and recursive methods.

---

## 1. Problem, Restated

Invert all `next` pointers in a singly linked list in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

- **Input**: Head of LinkedList / values to insert or delete.
- **Output**: Transformed LinkedList head / queried property.
- **Constraints**: Standard competitive programming limits ($0 \le N \le 10^5$).

---

## 2. Intuition & Pattern

Iterative: Maintain `prev = nullptr, curr = head`. In each step: save `front = curr->next`, reverse link `curr->next = prev`, slide `prev = curr`, `curr = front`. When `curr == nullptr`, `prev` is the new head. Recursive: reverse rest of list `newHead = reverse(head->next)`, link `head->next->next = head; head->next = nullptr;`.

- **Underlying Pattern**: `3-Pointer Sliding Window (`prev`, `curr`, `front`)`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Stack-based data copying in $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <stack>
using namespace std;
struct Node { int data; Node* next; };
Node* reverseStack(Node* head) {
    stack<int> st;
    Node* curr = head;
    while (curr) { st.push(curr->data); curr = curr->next; }
    curr = head;
    while (curr) { curr->data = st.top(); st.pop(); curr = curr->next; }
    return head;
}
```

### Java Code
```java
import java.util.*;

static class Node { int data; Node  next; };
Node  reverseStack(Node  head) {
    Stack<Integer> st = new Stack<>();
    Node  curr = head;
    while (curr) { st.push(curr.data); curr = curr.next; }
    curr = head;
    while (curr) { curr.data = st.peek(); st.pop(); curr = curr.next; }
    return head;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ stack memory.
- **Why it's not good enough**: Uses extra memory.

---

## 4. Approach 2 — Better

### Idea
Recursive reversal in O(N) stack space.

### C++17 Code
```cpp
struct Node { int data; Node* next; };
Node* reverseRecursive(Node* head) {
    if (!head || !head->next) return head;
    Node* newHead = reverseRecursive(head->next);
    head->next->next = head;
    head->next = nullptr;
    return newHead;
}
```

### Java Code
```java
static class Node { int data; Node  next; };
Node  reverseRecursive(Node  head) {
    if (!head || !head.next) return head;
    Node  newHead = reverseRecursive(head.next);
    head.next.next = head;
    head.next = null;
    return newHead;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ stack frames.
- **Why it's still not optimal**: Recursive call stack.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
3-Pointer Iterative In-Place Reversal in $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

class Solution {
public:
    Node* reverseList(Node* head) {
        Node* prev = nullptr;
        Node* curr = head;
        
        while (curr != nullptr) {
            Node* front = curr->next; // save next node
            curr->next = prev;        // reverse pointer
            prev = curr;              // slide prev forward
            curr = front;             // slide curr forward
        }
        
        return prev; // new head of reversed list
    }
};
```

### Java Code
```java
import java.util.*;

static class Node {
    int data;
    Node  next;
    public Node(int val) { /* initialized: data(val), next(null)  */  }
};

class Solution {

    Node  reverseList(Node  head) {
        Node  prev = null;
        Node  curr = head;
        
        while (curr != null) {
            Node  front = curr.next; // save next node
            curr.next = prev;        // reverse pointer
            prev = curr;              // slide prev forward
            curr = front;             // slide curr forward
        }
        
        return prev; // new head of reversed list
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Reverses pointers in-place without memory allocation.

---

## 6. Dry Run

`[1] -> [2] -> [3] -> NULL`

| Step | Action / State Change | Result |
|---|---|---|
| `Step 1` | curr = [1]: front=[2], [1]->next=null, prev=[1], curr=[2] | null <- [1] |
| `Step 2` | curr = [2]: front=[3], [2]->next=[1], prev=[2], curr=[3] | null <- [1] <- [2] |
| `Step 3` | curr = [3]: front=null, [3]->next=[2], prev=[3], curr=null | null <- [1] <- [2] <- [3] |
| `Exit` | curr == null -> return prev = [3] | `[3] -> [2] -> [1] -> NULL` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Empty list ($head == nullptr$).
- Single node list ($head->next == nullptr$).

### Common Bugs to Avoid
- Forgetting `head->next = nullptr` in recursive reversal (causes cyclic pointer loop).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does head->next->next = head work in recursive reversal?**  
  **A**: When `head` is at node 1 and `head->next` is node 2, after `reverse(2)` returns, node 2 is the TAIL of the reversed sublist. Setting `head->next->next = head` makes node 2 point back to node 1!


---

## 9. Tags & Related Problems

- **Tags**: `LinkedList`, `Pointers`, `In-Place`, `LeetCode-206`, `Easy`
- **Related problems to practice next**:
- **Palindrome LinkedList**: Reversing second half.
- **Reverse in K-Group**: Sublist reversal.
