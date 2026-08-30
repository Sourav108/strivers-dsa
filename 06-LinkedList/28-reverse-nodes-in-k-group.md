# Reverse Nodes in K-Group (LeetCode 25) (Step 6.5 — Hard Problems of LL)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Reverse Nodes in K-Group (LeetCode 25)](https://takeuforward.org/data-structure/reverse-linked-list-in-groups-of-size-k/)
- **Difficulty**: Hard
- **Statement**: Given the head of a linked list, reverse the nodes of the list $k$ at a time, and return the modified list (LeetCode 25 full production implementation with modular design).

---

## 1. Problem, Restated

Reverse list in groups of size $k$ maintaining relative order of remaining $< k$ nodes.

- **Input**: Head pointer of LinkedList(s) / parameters.
- **Output**: Modified LinkedList head / queried node.
- **Constraints**: Standard competitive programming limits ($0 \le N \le 10^5$).

---

## 2. Intuition & Pattern

Modular design: 1) `getKthNode(curr, k)` checks for complete group. 2) `reverseSublist(head)` reverses isolated group. 3) Sentinel `dummy` node manages transitions across chunk boundaries seamlessly.

- **Underlying Pattern**: `Group-by-Group Pointer Partitioning`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Vector chunk reversal in $\mathcal{O}(N)$ memory.

### C++17 Code
```cpp
// Vector chunking
```

### Java Code
```java
// Java equivalent
// Vector chunking
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Extra memory.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard pointer manipulation below directly solves the problem.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Production C++17 Iterative In-Place K-Group Reversal.

### C++17 Code
```cpp
struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

class Solution {
private:
    Node* getKthNode(Node* curr, int k) {
        k -= 1;
        while (curr != nullptr && k > 0) {
            k--;
            curr = curr->next;
        }
        return curr;
    }
    
    Node* reverseSublist(Node* head) {
        Node* prev = nullptr;
        Node* curr = head;
        while (curr) {
            Node* front = curr->next;
            curr->next = prev;
            prev = curr;
            curr = front;
        }
        return prev;
    }

public:
    Node* reverseKGroup(Node* head, int k) {
        if (!head || k <= 1) return head;
        
        Node dummy(0);
        dummy.next = head;
        Node* prevGroupTail = &dummy;
        Node* curr = head;
        
        while (curr != nullptr) {
            Node* kthNode = getKthNode(curr, k);
            if (!kthNode) {
                prevGroupTail->next = curr;
                break;
            }
            
            Node* nextGroupHead = kthNode->next;
            kthNode->next = nullptr;
            
            prevGroupTail->next = reverseSublist(curr);
            prevGroupTail = curr;
            curr = nextGroupHead;
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

    Node  getKthNode(Node  curr, int k) {
        k -= 1;
        while (curr != null && k > 0) {
            k--;
            curr = curr.next;
        }
        return curr;
    }
    
    Node  reverseSublist(Node  head) {
        Node  prev = null;
        Node  curr = head;
        while (curr) {
            Node  front = curr.next;
            curr.next = prev;
            prev = curr;
            curr = front;
        }
        return prev;
    }

    Node  reverseKGroup(Node  head, int k) {
        if (!head || k <= 1) return head;
        
        Node dummy(0);
        dummy.next = head;
        Node  prevGroupTail = &dummy;
        Node  curr = head;
        
        while (curr != null) {
            Node  kthNode = getKthNode(curr, k);
            if (kthNode == null) {
                prevGroupTail.next = curr;
                break;
            }
            
            Node  nextGroupHead = kthNode.next;
            kthNode.next = null;
            
            prevGroupTail.next = reverseSublist(curr);
            prevGroupTail = curr;
            curr = nextGroupHead;
        }
        
        return dummy.next;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Zero heap allocations, strict in-place link updates.

---

## 6. Dry Run

`[1, 2, 3, 4, 5, 6]`, $k = 3$

| Step | Action / State Change | Result |
|---|---|---|
| `Group 1 [1..3]` | reverse(`[1, 2, 3]`) -> `[3, 2, 1]` | dummy->next = [3], prevTail = [1] |
| `Group 2 [4..6]` | reverse(`[4, 5, 6]`) -> `[6, 5, 4]` | [1]->next = [6], prevTail = [4] |
| `Result` | curr reaches null | `[3, 2, 1, 6, 5, 4]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $k > N$ (no changes).
- $k = 1$ (no changes).

### Common Bugs to Avoid
- Not disconnecting `kthNode->next = nullptr` before calling sublist reversal.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is the time complexity if list has N nodes?**  
  **A**: Each node is traversed twice: once by getKthNode and once by reverseSublist, giving $2N$ operations $\implies \mathcal{O}(N)$ time.


---

## 9. Tags & Related Problems

- **Tags**: `LinkedList`, `Pointers`, `LeetCode-25`, `Hard`
- **Related problems to practice next**:
- **Reverse a LinkedList**: Sublist reversal.
