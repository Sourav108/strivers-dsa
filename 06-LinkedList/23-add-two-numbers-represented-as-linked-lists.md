# Add Two Numbers Represented by LinkedLists (Step 6.3 — Medium Problems of LL)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Add Two Numbers Represented by LinkedLists](https://takeuforward.org/data-structure/add-two-numbers-represented-as-linked-lists/)
- **Difficulty**: Medium
- **Statement**: You are given two non-empty linked lists representing two non-negative integers. The digits are stored in **reverse order**, and each node contains a single digit. Add the two numbers and return the sum as a linked list.

---

## 1. Problem, Restated

Add two numbers stored in reverse order node by node with carry.

- **Input**: Head pointer of LinkedList(s) / parameters.
- **Output**: Modified LinkedList head / queried node.
- **Constraints**: Standard competitive programming limits ($0 \le N \le 10^5$).

---

## 2. Intuition & Pattern

Since digits are stored in reverse order, head is the least significant digit (units place). Iterate while `l1 != nullptr || l2 != nullptr || carry > 0`: `sum = carry + (l1 ? l1->data : 0) + (l2 ? l2->data : 0)`. Create node with `sum % 10`, update `carry = sum / 10`.

- **Underlying Pattern**: `Dummy Head Pointer Addition with Carry Accumulator`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Converting linked lists to BigInt, adding, and converting back to LL.

### C++17 Code
```cpp
// BigInt conversion
```

### Java Code
```java
// Java equivalent
// BigInt conversion
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N + M)$.
- **Space Complexity**: $\mathcal{O}(N + M)$.
- **Why it's not good enough**: Unnecessary string parsing.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard pointer manipulation below directly solves the problem.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Single pass Dummy Head Addition with Carry in $\mathcal{O}(\max(N, M))$ time.

### C++17 Code
```cpp
struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

class Solution {
public:
    Node* addTwoNumbers(Node* l1, Node* l2) {
        Node dummy(0);
        Node* tail = &dummy;
        int carry = 0;
        
        while (l1 != nullptr || l2 != nullptr || carry > 0) {
            int sum = carry;
            if (l1) { sum += l1->data; l1 = l1->next; }
            if (l2) { sum += l2->data; l2 = l2->next; }
            
            carry = sum / 10;
            tail->next = new Node(sum % 10);
            tail = tail->next;
        }
        
        return dummy.next;
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

    Node  addTwoNumbers(Node  l1, Node  l2) {
        Node dummy(0);
        Node  tail = &dummy;
        int carry = 0;
        
        while (l1 != null || l2 != null || carry > 0) {
            int sum = carry;
            if (l1) { sum += l1.data; l1 = l1.next; }
            if (l2) { sum += l2.data; l2 = l2.next; }
            
            carry = sum / 10;
            tail.next = new Node(sum % 10);
            tail = tail.next;
        }
        
        return dummy.next;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\max(N, M))$ time.
- **Space Complexity**: $\mathcal{O}(\max(N, M))$ space for resultant list.
- **Why this is optimal**: Direct arithmetic addition in single pass.

---

## 6. Dry Run

`l1 = [2, 4, 3]` (342) + `l2 = [5, 6, 4]` (465)

| Step | Action / State Change | Result |
|---|---|---|
| `Units` | sum = 2 + 5 = 7 -> node = 7, carry = 0 | tail = [7] |
| `Tens` | sum = 4 + 6 = 10 -> node = 0, carry = 1 | tail = [0] |
| `Hundreds` | sum = 3 + 4 + 1 = 8 -> node = 8, carry = 0 | tail = [8] |
| `Result` | carry = 0 -> loop ends | `[7] -> [0] -> [8]` (807) ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Lists of unequal lengths.
- Final carry remaining (`[9, 9] + [1]` $\implies$ `[0, 0, 1]`).
- Zeros `[0] + [0]` $\implies$ `[0]`.

### Common Bugs to Avoid
- Forgetting `|| carry > 0` in while loop condition (drops most significant carry digit).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How to solve if numbers are stored in forward order (MSB first)?**  
  **A**: Use two stacks to reverse order, or reverse both lists, add, and reverse the result list (LeetCode 445: Add Two Numbers II).


---

## 9. Tags & Related Problems

- **Tags**: `LinkedList`, `Maths`, `LeetCode-2`, `Medium`
- **Related problems to practice next**:
- **Add 1 to LL**: Single increment.
- **Add Two Numbers II**: Forward order addition.
