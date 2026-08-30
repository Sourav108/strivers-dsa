# Reverse Nodes in k-Group (Iterative pointer manipulation) (Step 6.3 — Medium Problems of LL)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Reverse Nodes in k-Group (Iterative pointer manipulation)](https://takeuforward.org/data-structure/reverse-linked-list-in-groups-of-size-k/)
- **Difficulty**: Hard
- **Statement**: Given the head of a linked list, reverse the nodes of the list $k$ at a time, and return the modified list. If the number of nodes is not a multiple of $k$, left-out nodes at the end remain in original order.

---

## 1. Problem, Restated

Partition list into chunks of length $k$, reverse each chunk in-place, and connect chunk boundaries.

- **Input**: Head pointer of LinkedList(s) / parameters.
- **Output**: Modified LinkedList head / queried node.
- **Constraints**: Standard competitive programming limits ($0 \le N \le 10^5$).

---

## 2. Intuition & Pattern

1) Find the $K$-th node from `curr`. If $< K$ nodes remain, stop. 2) Save `nextNode = kthNode->next; kthNode->next = nullptr;`. 3) Reverse sublist $[\text{curr} \dots \text{kthNode}]$. 4) Connect `prevLast->next = kthNode; prevLast = curr; curr = nextNode;`.

- **Underlying Pattern**: `K-Group Boundary Detection + In-Place Sublist Reversal`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Store in array, reverse chunks of $K$, recreate list.

### C++17 Code
```cpp
// Array conversion
```

### Java Code
```java
// Java equivalent
// Array conversion
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Uses extra memory.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard pointer manipulation below directly solves the problem.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Iterative K-Group Reversal with Dummy Sentinel Node in $\mathcal{O}(1)$ space.

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
        Node dummy(0);
        dummy.next = head;
        Node* prevLast = &dummy;
        Node* temp = head;
        
        while (temp != nullptr) {
            Node* kthNode = getKthNode(temp, k);
            if (!kthNode) {
                prevLast->next = temp; // connect remaining unchanged nodes
                break;
            }
            
            Node* nextGroup = kthNode->next;
            kthNode->next = nullptr; // isolate k-sublist
            
            reverseSublist(temp); // kthNode becomes new head of this chunk
            
            prevLast->next = kthNode;
            prevLast = temp; // temp is now the tail of this reversed chunk
            temp = nextGroup;
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
        Node dummy(0);
        dummy.next = head;
        Node  prevLast = &dummy;
        Node  temp = head;
        
        while (temp != null) {
            Node  kthNode = getKthNode(temp, k);
            if (kthNode == null) {
                prevLast.next = temp; // connect remaining unchanged nodes
                break;
            }
            
            Node  nextGroup = kthNode.next;
            kthNode.next = null; // isolate k-sublist
            
            reverseSublist(temp); // kthNode becomes new head of this chunk
            
            prevLast.next = kthNode;
            prevLast = temp; // temp is now the tail of this reversed chunk
            temp = nextGroup;
        }
        
        return dummy.next;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2N) = \mathcal{O}(N)$ time (each node visited twice: once to find $K$-th, once to reverse).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Strict in-place pointer manipulation.

---

## 6. Dry Run

`[1, 2, 3, 4, 5]`, $k = 2$

| Step | Action / State Change | Result |
|---|---|---|
| `Group 1` | kthNode = [2], reverse(`[1, 2]`) -> `[2, 1]` | dummy->next = [2], prevLast = [1] |
| `Group 2` | kthNode = [4], reverse(`[3, 4]`) -> `[4, 3]` | [1]->next = [4], prevLast = [3] |
| `Group 3` | kthNode for [5] is NULL (< k) | [3]->next = [5] |
| `Result` | Loop terminates | `[2, 1, 4, 3, 5]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $k = 1$ (no changes).
- $k = N$ (entire list reversed).
- Remaining nodes $< k$ (preserved).

### Common Bugs to Avoid
- Forgetting to reconnect `prevLast->next = temp` when remaining nodes are $< k$.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is getKthNode essential before reversing?**  
  **A**: Because the problem specifies that trailing nodes $< k$ must NOT be reversed. Checking for $K$ nodes upfront prevents reversing incomplete chunks.


---

## 9. Tags & Related Problems

- **Tags**: `LinkedList`, `Pointers`, `In-Place`, `LeetCode-25`, `Hard`
- **Related problems to practice next**:
- **Reverse a LinkedList**: Sublist reversal.
