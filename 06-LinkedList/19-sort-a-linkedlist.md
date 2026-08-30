# Sort a LinkedList (Merge Sort on LL) (Step 6.3 — Medium Problems of LL)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Sort a LinkedList (Merge Sort on LL)](https://takeuforward.org/data-structure/sort-a-linked-list/)
- **Difficulty**: Medium
- **Statement**: Given the head of a linked list, return the list after sorting it in ascending order in $\mathcal{O}(N \log N)$ time and $\mathcal{O}(\log N)$ stack space (Merge Sort on Linked List).

---

## 1. Problem, Restated

Implement divide-and-conquer Merge Sort on Linked List nodes.

- **Input**: Head pointer of LinkedList(s) / parameters.
- **Output**: Modified LinkedList head / queried node.
- **Constraints**: Standard competitive programming limits ($0 \le N \le 10^5$).

---

## 2. Intuition & Pattern

1) Find middle node using fast-slow pointers (`fast = head->next`). 2) Split list into two halves: `left = head, right = mid->next; mid->next = nullptr;`. 3) Recursively sort both halves `sortList(left), sortList(right)`. 4) Merge the two sorted lists using dummy head in $\mathcal{O}(N)$. Total time: $\mathcal{O}(N \log N)$.

- **Underlying Pattern**: `Divide and Conquer: Midpoint Split + Merge Two Sorted Lists`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Copy to vector, `std::sort`, copy back in $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;
struct Node { int data; Node* next; };
Node* sortArray(Node* head) {
    vector<int> v;
    Node* c = head;
    while (c) { v.push_back(c->data); c = c->next; }
    sort(v.begin(), v.end());
    c = head;
    for (int x : v) { c->data = x; c = c->next; }
    return head;
}
```

### Java Code
```java
import java.util.*;

static class Node { int data; Node  next; };
Node  sortArray(Node  head) {
    List<Integer> v = new ArrayList<>();
    Node  c = head;
    while (c) { v.add(c.data); c = c.next; }
    Arrays.sort(v);
    c = head;
    for (int x : v) { c.data = x; c = c.next; }
    return head;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ vector memory.
- **Why it's not good enough**: Uses extra memory.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard pointer manipulation below directly solves the problem.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Recursive Merge Sort with Tortoise-Hare Midpoint Split.

### C++17 Code
```cpp
struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

class Solution {
private:
    Node* findMid(Node* head) {
        Node* slow = head;
        Node* fast = head->next; // stops slow at first middle
        while (fast != nullptr && fast->next != nullptr) {
            slow = slow->next;
            fast = fast->next->next;
        }
        return slow;
    }
    
    Node* merge(Node* l1, Node* l2) {
        Node dummy(0);
        Node* tail = &dummy;
        
        while (l1 && l2) {
            if (l1->data <= l2->data) {
                tail->next = l1;
                l1 = l1->next;
            } else {
                tail->next = l2;
                l2 = l2->next;
            }
            tail = tail->next;
        }
        tail->next = (l1) ? l1 : l2;
        return dummy.next;
    }

public:
    Node* sortList(Node* head) {
        if (!head || !head->next) return head;
        
        // 1. Split list at middle
        Node* mid = findMid(head);
        Node* rightHead = mid->next;
        mid->next = nullptr; // break link
        
        // 2. Recursively sort halves
        Node* left = sortList(head);
        Node* right = sortList(rightHead);
        
        // 3. Merge sorted halves
        return merge(left, right);
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

    Node  findMid(Node  head) {
        Node  slow = head;
        Node  fast = head.next; // stops slow at first middle
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        return slow;
    }
    
    Node  merge(Node  l1, Node  l2) {
        Node dummy(0);
        Node  tail = &dummy;
        
        while (l1 && l2) {
            if (l1.data <= l2.data) {
                tail.next = l1;
                l1 = l1.next;
            } else {
                tail.next = l2;
                l2 = l2.next;
            }
            tail = tail.next;
        }
        tail.next = (l1) ? l1 : l2;
        return dummy.next;
    }

    Node  sortList(Node  head) {
        if (!head || !head.next) return head;
        
        // 1. Split list at middle
        Node  mid = findMid(head);
        Node  rightHead = mid.next;
        mid.next = null; // break link
        
        // 2. Recursively sort halves
        Node  left = sortList(head);
        Node  right = sortList(rightHead);
        
        // 3. Merge sorted halves
        return merge(left, right);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N)$ guaranteed time.
- **Space Complexity**: $\mathcal{O}(\log N)$ recursion call stack space.
- **Why this is optimal**: Merge sort on linked lists requires $\mathcal{O}(1)$ extra memory per merge step (unlike arrays which require $\mathcal{O}(N)$ scratch buffers).

---

## 6. Dry Run

`[4, 2, 1, 3]`

| Step | Action / State Change | Result |
|---|---|---|
| `Split 1` | mid = [2], left = `[4, 2]`, right = `[1, 3]` | mid->next = null |
| `Sort Halves` | sort(`[4, 2]`) -> `[2, 4]`, sort(`[1, 3]`) -> `[1, 3]` | Recursive returns |
| `Merge` | merge(`[2, 4]`, `[1, 3]`) | `[1, 2, 3, 4]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N \le 1$ (base case returns head).
- Already sorted list.

### Common Bugs to Avoid
- Using `fast = head` in findMid (causes infinite recursion on 2-element lists because `mid` never splits). Must use `fast = head->next`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is Merge Sort preferred over QuickSort for LinkedLists?**  
  **A**: Because Merge Sort on linked lists requires $\mathcal{O}(1)$ extra auxiliary memory to merge (just pointer re-linking), and provides guaranteed $\mathcal{O}(N \log N)$ worst-case time.


---

## 9. Tags & Related Problems

- **Tags**: `LinkedList`, `Sorting`, `Merge Sort`, `LeetCode-148`, `Medium`
- **Related problems to practice next**:
- **Merge Two Sorted Lists**: Merge helper.
