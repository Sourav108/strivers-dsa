# Find Pairs with Given Sum in Sorted DLL (Step 6.4 — Medium Problems of DLL)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Find Pairs with Given Sum in Sorted DLL](https://takeuforward.org/data-structure/find-pairs-with-given-sum-in-dll/)
- **Difficulty**: Easy
- **Statement**: Given a sorted Doubly Linked List of distinct positive integers and a target `sum`, find all pairs of nodes whose values add up to `sum` in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ auxiliary space.

---

## 1. Problem, Restated

Two-pointer bidirectional collision scan on sorted DLL.

- **Input**: Head pointer of LinkedList(s) / parameters.
- **Output**: Modified LinkedList head / queried node.
- **Constraints**: Standard competitive programming limits ($0 \le N \le 10^5$).

---

## 2. Intuition & Pattern

Since the DLL is sorted and supports bidirectional traversal: 1) Find the tail node `right`. 2) Initialize `left = head`. 3) While `left->data < right->data`: if `left->data + right->data == sum`, record pair, `left = left->next, right = right->prev`. If sum $< target$, `left = left->next`. If sum $> target$, `right = right->prev`.

- **Underlying Pattern**: `Bidirectional Two Pointers (`left = head, right = tail`)`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Nested loops checking all $N^2$ node pairs in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
// O(N^2) nested loops
```

### Java Code
```java
// Java equivalent
// O(N^2) nested loops
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Quadratic comparisons.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard pointer manipulation below directly solves the problem.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Two-Pointer Left/Right Traversal on DLL in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
#include <vector>
using namespace std;

struct Node {
    int data;
    Node* next;
    Node* prev;
    Node(int val) : data(val), next(nullptr), prev(nullptr) {}
};

class Solution {
public:
    vector<pair<int, int>> findPairsWithGivenSum(Node *head, int target) {
        vector<pair<int, int>> pairs;
        if (!head || !head->next) return pairs;
        
        // Step 1: Find tail
        Node* left = head;
        Node* right = head;
        while (right->next != nullptr) right = right->next;
        
        // Step 2: Two-pointer scan
        while (left != nullptr && right != nullptr && left->data < right->data) {
            int currentSum = left->data + right->data;
            if (currentSum == target) {
                pairs.push_back({left->data, right->data});
                left = left->next;
                right = right->prev;
            } else if (currentSum < target) {
                left = left->next;
            } else {
                right = right->prev;
            }
        }
        
        return pairs;
    }
};
```

### Java Code
```java
import java.util.*;

static class Node {
    int data;
    Node  next;
    Node  prev;
    public Node(int val) { /* initialized: data(val), next(null), prev(null)  */  }
};

class Solution {

    List<int[]> findPairsWithGivenSum(Node head, int target) {
        List<int[]> pairs;
        if (!head || !head.next) return pairs;
        
        // Step 1: Find tail
        Node  left = head;
        Node  right = head;
        while (right.next != null) right = right.next;
        
        // Step 2: Two-pointer scan
        while (left != null && right != null && left.data < right.data) {
            int currentSum = left.data + right.data;
            if (currentSum == target) {
                pairs.add({left.data, right.data});
                left = left.next;
                right = right.prev;
            } else if (currentSum < target) {
                left = left.next;
            } else {
                right = right.prev;
            }
        }
        
        return pairs;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time ($N$ to find tail + $N$ two-pointer traversal).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Sorted property allows monotonic pointer adjustments without array conversion.

---

## 6. Dry Run

`[1] <-> [2] <-> [4] <-> [5] <-> [6] <-> [8] <-> [9]`, `target = 7`

| Step | Action / State Change | Result |
|---|---|---|
| `left=1, right=9` | 1 + 9 = 10 > 7 -> right = 8 | right moves left |
| `left=1, right=8` | 1 + 8 = 9 > 7 -> right = 6 | right moves left |
| `left=1, right=6` | 1 + 6 = 7 == 7 -> Pair (1, 6)! | left=2, right=5 |
| `left=2, right=5` | 2 + 5 = 7 == 7 -> Pair (2, 5)! | left=4, right=4 |
| `left=4, right=4` | left->data >= right->data -> loop terminates | Pairs: `[(1,6), (2,5)]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- No valid pairs (returns empty vector).
- Single node DLL.

### Common Bugs to Avoid
- Condition `left != right` can overshoot if pointers cross each other (use `left->data < right->data` for strictly sorted positive DLLs).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is DLL required for this Two-Pointer technique?**  
  **A**: Because SLL only supports forward traversal. A DLL allows `right = right->prev` in $\mathcal{O}(1)$ time without resetting from the head.


---

## 9. Tags & Related Problems

- **Tags**: `Doubly LinkedList`, `Two Pointers`, `Easy`
- **Related problems to practice next**:
- **Two Sum (Arrays)**: Array counterpart.
