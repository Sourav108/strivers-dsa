# Segregate Odd and Even Nodes in LinkedList (Step 6.3 — Medium Problems of LL)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Segregate Odd and Even Nodes in LinkedList](https://takeuforward.org/data-structure/segregate-even-and-odd-nodes-in-linkedlist/)
- **Difficulty**: Medium
- **Statement**: Given the head of a singly linked list, group all nodes with odd indices together followed by the nodes with even indices (indices are 1-based, not node values), preserving relative node order in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

---

## 1. Problem, Restated

Separate odd-indexed and even-indexed nodes into two interwoven chains, then connect `oddTail->next = evenHead`.

- **Input**: Head pointer of LinkedList(s) / parameters.
- **Output**: Modified LinkedList head / queried node.
- **Constraints**: Standard competitive programming limits ($0 \le N \le 10^5$).

---

## 2. Intuition & Pattern

Maintain `odd = head` and `even = head->next`, saving `evenHead = even`. Loop while `even != nullptr && even->next != nullptr`: set `odd->next = even->next; odd = odd->next;`, set `even->next = odd->next; even = even->next;`. Finally, link `odd->next = evenHead`.

- **Underlying Pattern**: `Two-Pointer Chain Interleaving (`odd` and `even`)`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Collect odd and even nodes in two vectors and reconstruct list.

### C++17 Code
```cpp
#include <vector>
using namespace std;
struct Node { int data; Node* next; };
Node* oddEvenBrute(Node* head) {
    if (!head) return nullptr;
    vector<int> odds, evens;
    Node* curr = head;
    int idx = 1;
    while (curr) {
        if (idx % 2 != 0) odds.push_back(curr->data);
        else evens.push_back(curr->data);
        idx++;
        curr = curr->next;
    }
    curr = head;
    for (int x : odds) { curr->data = x; curr = curr->next; }
    for (int x : evens) { curr->data = x; curr = curr->next; }
    return head;
}
```

### Java Code
```java
static class Node { int data; Node  next; };
Node  oddEvenBrute(Node  head) {
    if (head == null) return null;
    int[] odds, evens;
    Node  curr = head;
    int idx = 1;
    while (curr) {
        if (idx % 2 != 0) odds.add(curr.data);
        else evens.add(curr.data);
        idx++;
        curr = curr.next;
    }
    curr = head;
    for (int x : odds) { curr.data = x; curr = curr.next; }
    for (int x : evens) { curr.data = x; curr = curr.next; }
    return head;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ vector storage.
- **Why it's not good enough**: Uses extra memory.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard pointer manipulation below directly solves the problem.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
In-place pointer separation and reconnection in $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

class Solution {
public:
    Node* oddEvenList(Node* head) {
        if (!head || !head->next) return head;
        
        Node* odd = head;
        Node* even = head->next;
        Node* evenHead = even; // save even list start
        
        while (even != nullptr && even->next != nullptr) {
            odd->next = even->next;
            odd = odd->next;
            
            even->next = odd->next;
            even = even->next;
        }
        
        // Connect end of odd list to head of even list
        odd->next = evenHead;
        
        return head;
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

    Node  oddEvenList(Node  head) {
        if (!head || !head.next) return head;
        
        Node  odd = head;
        Node  even = head.next;
        Node  evenHead = even; // save even list start
        
        while (even != null && even.next != null) {
            odd.next = even.next;
            odd = odd.next;
            
            even.next = odd.next;
            even = even.next;
        }
        
        // Connect end of odd list to head of even list
        odd.next = evenHead;
        
        return head;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Re-links pointers in-place without data copying or memory allocation.

---

## 6. Dry Run

`[1] -> [2] -> [3] -> [4] -> [5]`

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | odd = [1], even = [2], evenHead = [2] | Start |
| `Iter 1` | odd->next = [3] (odd=[3]), even->next = [4] (even=[4]) | odds: `[1->3]`, evens: `[2->4]` |
| `Iter 2` | odd->next = [5] (odd=[5]), even->next = null (even=null) | odds: `[1->3->5]`, evens: `[2->4]` |
| `Connect` | odd->next = evenHead ([5]->next = [2]) | `[1] -> [3] -> [5] -> [2] -> [4] -> NULL` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N \le 2$ (returns head unchanged).
- Even length list `[1, 2, 3, 4]`.

### Common Bugs to Avoid
- Loop condition must be `while (even != nullptr && even->next != nullptr)` because `even` is ahead of `odd`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does tracking even pointer control loop termination?**  
  **A**: Because `even` is strictly ahead of `odd`. When `even` reaches null (odd length) or `even->next` reaches null (even length), all nodes have been partitioned.


---

## 9. Tags & Related Problems

- **Tags**: `LinkedList`, `Two Pointers`, `In-Place`, `LeetCode-328`, `Medium`
- **Related problems to practice next**:
- **Sort 0s 1s 2s in LL**: Multi-pointer partitioning.
