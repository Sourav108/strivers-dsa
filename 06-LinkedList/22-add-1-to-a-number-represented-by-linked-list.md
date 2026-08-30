# Add 1 to a Number Represented by LinkedList (Step 6.3 — Medium Problems of LL)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Add 1 to a Number Represented by LinkedList](https://takeuforward.org/data-structure/add-1-to-a-number-represented-by-linked-list/)
- **Difficulty**: Medium
- **Statement**: A number $N$ is represented in a linked list such that each node contains a single digit. Add 1 to the number and return the head of the modified list.

---

## 1. Problem, Restated

Propagate carry $+1$ from right to left using backtracking recursion or list reversal.

- **Input**: Head pointer of LinkedList(s) / parameters.
- **Output**: Modified LinkedList head / queried node.
- **Constraints**: Standard competitive programming limits ($0 \le N \le 10^5$).

---

## 2. Intuition & Pattern

Because addition proceeds from least significant digit (tail) to most significant digit (head), we can use **backtracking recursion**: traverse to the end of the list. On returning, compute `sum = curr->data + carry`, update `curr->data = sum % 10`, and return `carry = sum / 10`. If the root call returns `carry == 1` (e.g. `999 + 1 = 1000`), prepend a new head node with value 1.

- **Underlying Pattern**: `Backtracking Call Stack Carry Propagation`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Reverse list, add 1 iteratively with carry, reverse back in $\mathcal{O}(3N)$ time.

### C++17 Code
```cpp
// Double reversal approach
```

### Java Code
```java
// Java equivalent
// Double reversal approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(3N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Three full list traversals.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard pointer manipulation below directly solves the problem.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Backtracking Recursion Carry Propagation in a single pass.

### C++17 Code
```cpp
struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

class Solution {
private:
    int addCarryHelper(Node* curr) {
        if (!curr) return 1; // base carry = 1 to add
        
        int carry = addCarryHelper(curr->next);
        int sum = curr->data + carry;
        curr->data = sum % 10;
        return sum / 10; // return new carry
    }

public:
    Node* addOne(Node *head) {
        int carry = addCarryHelper(head);
        
        // If carry remains at most significant digit (e.g. 999 -> 1000)
        if (carry == 1) {
            Node* newHead = new Node(1);
            newHead->next = head;
            return newHead;
        }
        
        return head;
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

    int addCarryHelper(Node  curr) {
        if (curr == null) return 1; // base carry = 1 to add
        
        int carry = addCarryHelper(curr.next);
        int sum = curr.data + carry;
        curr.data = sum % 10;
        return sum / 10; // return new carry
    }

    Node  addOne(Node head) {
        int carry = addCarryHelper(head);
        
        // If carry remains at most significant digit (e.g. 999 . 1000)
        if (carry == 1) {
            Node  newHead = new Node(1);
            newHead.next = head;
            return newHead;
        }
        
        return head;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(N)$ recursion call stack space.
- **Why this is optimal**: LIFO call stack unwinds from tail to head naturally.

---

## 6. Dry Run

`[9] -> [9] -> [9]` + 1

| Step | Action / State Change | Result |
|---|---|---|
| `Tail [9]` | sum = 9 + 1 = 10 -> node = 0, carry = 1 | [0] |
| `Mid [9]` | sum = 9 + 1 = 10 -> node = 0, carry = 1 | [0] -> [0] |
| `Head [9]` | sum = 9 + 1 = 10 -> node = 0, carry = 1 | [0] -> [0] -> [0] |
| `Root Carry` | carry == 1 -> Prepend new Node(1) | `[1] -> [0] -> [0] -> [0]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- All 9s `[9, 9, 9]` $\implies$ `[1, 0, 0, 0]`.
- No carry propagation `[4, 5, 6]` $\implies$ `[4, 5, 7]`.

### Common Bugs to Avoid
- Forgetting to prepend new node when carry reaches root.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How to achieve O(1) space without recursion stack?**  
  **A**: Find the rightmost non-9 node. Increment it by 1, and set all subsequent nodes to 0. If all nodes are 9, prepend a new node 1!


---

## 9. Tags & Related Problems

- **Tags**: `LinkedList`, `Recursion`, `Backtracking`, `Medium`
- **Related problems to practice next**:
- **Add Two Numbers**: General addition.
