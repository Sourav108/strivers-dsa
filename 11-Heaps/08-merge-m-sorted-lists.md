# Merge M Sorted Lists (Min-Heap optimization) (Step 11.2 — Medium Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Merge M Sorted Lists (Min-Heap optimization)](https://takeuforward.org/data-structure/merge-m-sorted-lists/)
- **Difficulty**: Hard
- **Statement**: You are given an array of $k$ linked-lists `lists`, each linked-list is sorted in ascending order. Merge all the linked-lists into one sorted linked-list and return it in $\mathcal{O}(N \log K)$ time.

---

## 1. Problem, Restated

Multi-way merge of $K$ sorted lists using a Min-Heap of size $K$.

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Value / Top-K elements / Merged list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Maintain a Min-Heap storing the current head nodes of all $K$ lists. 1) Push non-null heads of all $K$ lists into `minHeap`. 2) Extract minimum node `curr = minHeap.top(); minHeap.pop();`. 3) Append `curr` to merged list. 4) If `curr->next != nullptr`, push `curr->next` into `minHeap`. Repeat until heap is empty! Total time: $\mathcal{O}(N \log K)$ where $N$ is total nodes.

- **Underlying Pattern**: `K-Way Min-Heap Head Pointer Merge`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Dump all nodes into a vector, sort, and rebuild list in $\mathcal{O}(N \log N)$ time and $\mathcal{O}(N)$ memory.

### C++17 Code
```cpp
// Vector dump and sort
```

### Java Code
```java
import java.util.*;

class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        if (lists == null || lists.length == 0) return null;
        
        PriorityQueue<ListNode> minHeap = new PriorityQueue<>((a, b) -> Integer.compare(a.val, b.val));
        
        // Push head of each non-empty list
        for (ListNode node : lists) {
            if (node != null) {
                minHeap.offer(node);
            }
        }
        
        ListNode dummy = new ListNode(0);
        ListNode tail = dummy;
        
        while (!minHeap.isEmpty()) {
            ListNode curr = minHeap.poll();
            tail.next = curr;
            tail = tail.next;
            
            if (curr.next != null) {
                minHeap.offer(curr.next);
            }
        }
        
        return dummy.next;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Doesn't exploit pre-sorted individual lists.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Priority Queue / Heap implementation below directly achieves optimal $\mathcal{O}(N \log K)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
K-Way Min-Heap in $\mathcal{O}(N \log K)$ time and $\mathcal{O}(K)$ auxiliary space.

### C++17 Code
```cpp
#include <vector>
#include <queue>
using namespace std;

struct ListNode {
    int val;
    ListNode *next;
    ListNode(int x) : val(x), next(nullptr) {}
};

struct CompareNode {
    bool operator()(ListNode* a, ListNode* b) {
        return a->val > b->val; // Min-heap comparator
    }
};

class Solution {
public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        priority_queue<ListNode*, vector<ListNode*>, CompareNode> minHeap;
        
        // Push initial head of each non-empty list
        for (ListNode* head : lists) {
            if (head != nullptr) {
                minHeap.push(head);
            }
        }
        
        ListNode dummy(0);
        ListNode* tail = &dummy;
        
        while (!minHeap.empty()) {
            ListNode* smallest = minHeap.top();
            minHeap.pop();
            
            tail->next = smallest;
            tail = tail->next;
            
            if (smallest->next != nullptr) {
                minHeap.push(smallest->next);
            }
        }
        
        return dummy.next;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        if (lists == null || lists.length == 0) return null;
        
        PriorityQueue<ListNode> minHeap = new PriorityQueue<>((a, b) -> Integer.compare(a.val, b.val));
        
        // Push head of each non-empty list
        for (ListNode node : lists) {
            if (node != null) {
                minHeap.offer(node);
            }
        }
        
        ListNode dummy = new ListNode(0);
        ListNode tail = dummy;
        
        while (!minHeap.isEmpty()) {
            ListNode curr = minHeap.poll();
            tail.next = curr;
            tail = tail.next;
            
            if (curr.next != null) {
                minHeap.offer(curr.next);
            }
        }
        
        return dummy.next;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log K)$ time where $N$ is total nodes across all $K$ lists.
- **Space Complexity**: $\mathcal{O}(K)$ heap space.
- **Why this is optimal**: Min-heap never holds more than $K$ nodes at any instant.

---

## 6. Dry Run

`lists = [[1, 4, 5], [1, 3, 4], [2, 6]]` ($K = 3$)

| Step | Action / State Change | Result |
|---|---|---|
| `Init heap` | push 1, 1, 2 -> minHeap: `[1, 1, 2]` | tail -> dummy |
| `Pop 1 (list 0)` | tail -> 1, push 4 -> minHeap: `[1, 2, 4]` | `1` |
| `Pop 1 (list 1)` | tail -> 1 -> 1, push 3 -> minHeap: `[2, 3, 4]` | `1 -> 1` |
| `Pop 2 (list 2)` | tail -> 1 -> 1 -> 2, push 6 -> minHeap: `[3, 4, 6]` | `1 -> 1 -> 2` |
| `Continues...` | Merges in sorted order | Complete: `1->1->2->3->4->4->5->6` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `lists` is empty `[]` (returns `nullptr`).
- All lists are `nullptr` `[[], []]`.

### Common Bugs to Avoid
- Missing custom comparator struct for `ListNode*`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Can this be solved without a heap in O(N log K)?**  
  **A**: Yes! Using **Divide and Conquer** (Merge Sort pairing): pair up $K$ lists and merge 2 at a time iteratively. Total time is also $\mathcal{O}(N \log K)$ with $\mathcal{O}(1)$ extra space.


---

## 9. Tags & Related Problems

- **Tags**: `Heap`, `Priority Queue`, `LinkedList`, `LeetCode-23`, `Hard`
- **Related problems to practice next**:
- **Merge Two Sorted Lists**: 2-way base case.
