# Check if LinkedList is Palindrome (Step 6.3 — Medium Problems of LL)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Check if LinkedList is Palindrome](https://takeuforward.org/data-structure/check-if-given-linked-list-is-palindrome/)
- **Difficulty**: Easy
- **Statement**: Given the head of a singly linked list, return `true` if it is a palindrome or `false` otherwise in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ extra memory.

---

## 1. Problem, Restated

Determine if linked list values are symmetrical without using extra array or stack space.

- **Input**: Head of LinkedList / values to insert or delete.
- **Output**: Transformed LinkedList head / queried property.
- **Constraints**: Standard competitive programming limits ($0 \le N \le 10^5$).

---

## 2. Intuition & Pattern

1) Find the middle node using Tortoise & Hare (`slow`, `fast`). 2) Reverse the second half of the list starting from `slow->next`. 3) Compare the first half (`head`) and reversed second half (`newHead`). 4) (Optional) Restore the original list structure by re-reversing.

- **Underlying Pattern**: `Tortoise-Hare Midpoint + Reverse Second Half + Compare`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Push values into vector and check palindrome in $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <vector>
using namespace std;
struct Node { int data; Node* next; };
bool isPalArray(Node* head) {
    vector<int> v;
    while (head) { v.push_back(head->data); head = head->next; }
    int l = 0, r = v.size() - 1;
    while (l < r) if (v[l++] != v[r--]) return false;
    return true;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ vector space.
- **Why it's not good enough**: Uses extra memory.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard pointer manipulation below directly solves the problem.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
In-place reversal of second half in $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
struct Node {
    int data;
    Node* next;
    Node(int val) : data(val), next(nullptr) {}
};

class Solution {
private:
    Node* reverseList(Node* head) {
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
    bool isPalindrome(Node* head) {
        if (!head || !head->next) return true;
        
        // 1. Find midpoint (first middle for even length)
        Node* slow = head;
        Node* fast = head;
        while (fast->next != nullptr && fast->next->next != nullptr) {
            slow = slow->next;
            fast = fast->next->next;
        }
        
        // 2. Reverse second half
        Node* secondHalfHead = reverseList(slow->next);
        
        // 3. Compare halves
        Node* firstHalf = head;
        Node* secondHalf = secondHalfHead;
        bool isPal = true;
        
        while (secondHalf != nullptr) {
            if (firstHalf->data != secondHalf->data) {
                isPal = false;
                break;
            }
            firstHalf = firstHalf->next;
            secondHalf = secondHalf->next;
        }
        
        // 4. Restore original list
        slow->next = reverseList(secondHalfHead);
        
        return isPal;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time ($N/2$ find mid + $N/2$ reverse + $N/2$ compare + $N/2$ restore).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Operates entirely in-place on existing node links.

---

## 6. Dry Run

`[1] -> [2] -> [2] -> [1]`

| Step | Action / State Change | Result |
|---|---|---|
| `Midpoint` | slow points to first [2] | First half: `[1, 2]`, Second half: `[2, 1]` |
| `Reverse 2nd half` | reverse(`[2, 1]`) -> `[1, 2]` | secondHalfHead = `[1, 2]` |
| `Compare` | 1 == 1 and 2 == 2 | All match -> isPal = true |
| `Restore` | Re-reverse second half | Returns TRUE ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single node ($N=1$ -> true).
- Odd length list `[1, 2, 3, 2, 1]` (second half comparison stops when second half ends).

### Common Bugs to Avoid
- Using `fast != nullptr && fast->next != nullptr` for midpoint (for even length lists, you must stop at the FIRST middle to split halves cleanly).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why must we use fast->next != nullptr && fast->next->next != nullptr?**  
  **A**: For even length lists like `[1, 2, 2, 1]`, this stops `slow` at the first `2`, so `slow->next` correctly begins the exact second half `[2, 1]`.


---

## 9. Tags & Related Problems

- **Tags**: `LinkedList`, `Two Pointers`, `Fast Slow`, `LeetCode-234`, `Easy`
- **Related problems to practice next**:
- **Find Middle Node**: Midpoint search.
- **Reverse a LinkedList**: Sublist reversal.
