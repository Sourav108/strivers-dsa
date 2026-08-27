# Clone a LinkedList with Random and Next Pointer (O(1) space) (Step 6.5 — Hard Problems of LL)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Clone a LinkedList with Random and Next Pointer (O(1) space)](https://takeuforward.org/data-structure/clone-linked-list-with-random-and-next-pointer/)
- **Difficulty**: Hard
- **Statement**: A linked list of length $n$ is given such that each node contains an additional random pointer, which could point to any node in the list, or `null`. Construct a deep copy of the list in $\mathcal{O}(N)$ time and strictly $\mathcal{O}(1)$ auxiliary space.

---

## 1. Problem, Restated

Clone a complex linked list with random pointers without using an auxiliary hash map.

- **Input**: Head pointer of LinkedList(s) / parameters.
- **Output**: Modified LinkedList head / queried node.
- **Constraints**: Standard competitive programming limits ($0 \le N \le 10^5$).

---

## 2. Intuition & Pattern

Step 1 (Interweave): For each node $A$, insert its cloned copy $A'$ immediately after it: $A \to A' \to B \to B'$. Step 2 (Copy Random): Set $A'\to\text{random} = A\to\text{random} \to \text{next}$. Step 3 (Separate Lists): Restore original list and extract cloned list $A' \to B'$.

- **Underlying Pattern**: `3-Pass Interweaving (Node Copy Injection $\to$ Random Pointer Link $\to$ De-interweaving)`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Hash Map mapping original nodes to cloned nodes `unordered_map<Node*, Node*>` in $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <unordered_map>
using namespace std;
struct Node { int val; Node* next; Node* random; Node(int v): val(v), next(nullptr), random(nullptr){} };
Node* copyRandomListHash(Node* head) {
    if (!head) return nullptr;
    unordered_map<Node*, Node*> mp;
    Node* curr = head;
    while (curr) { mp[curr] = new Node(curr->val); curr = curr->next; }
    curr = head;
    while (curr) {
        mp[curr]->next = mp[curr->next];
        mp[curr]->random = mp[curr->random];
        curr = curr->next;
    }
    return mp[head];
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ hash map space.
- **Why it's not good enough**: Hash map memory overhead.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard pointer manipulation below directly solves the problem.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
3-Pass In-Place Node Interweaving in $\mathcal{O}(1)$ auxiliary space.

### C++17 Code
```cpp
struct Node {
    int val;
    Node* next;
    Node* random;
    Node(int _val) : val(_val), next(nullptr), random(nullptr) {}
};

class Solution {
public:
    Node* copyRandomList(Node* head) {
        if (!head) return nullptr;
        
        // Pass 1: Create cloned nodes and interweave: A -> A' -> B -> B'
        Node* curr = head;
        while (curr != nullptr) {
            Node* copy = new Node(curr->val);
            copy->next = curr->next;
            curr->next = copy;
            curr = copy->next;
        }
        
        // Pass 2: Connect random pointers for cloned nodes
        curr = head;
        while (curr != nullptr) {
            if (curr->random != nullptr) {
                curr->next->random = curr->random->next;
            }
            curr = curr->next->next;
        }
        
        // Pass 3: Separate original list and cloned list
        curr = head;
        Node* cloneHead = head->next;
        Node* copyCurr = cloneHead;
        
        while (curr != nullptr) {
            curr->next = curr->next->next;
            if (copyCurr->next != nullptr) {
                copyCurr->next = copyCurr->next->next;
            }
            curr = curr->next;
            copyCurr = copyCurr->next;
        }
        
        return cloneHead;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(3N) = \mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space (excluding memory for the new clone nodes).
- **Why this is optimal**: Node interweaving provides $\mathcal{O}(1)$ address mapping without hash table overhead.

---

## 6. Dry Run

List: `[A] -> [B]`, `A->random = B`, `B->random = A`

| Step | Action / State Change | Result |
|---|---|---|
| `Pass 1 (Interweave)` | `[A] -> [A'] -> [B] -> [B']` | Clones interleaved |
| `Pass 2 (Random)` | A'->random = A->random->next = B' | Random pointers mapped |
| `Pass 3 (Separate)` | Restore `[A] -> [B]`, extract `[A'] -> [B']` | Clone deep copy returned ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `head == nullptr` (returns `nullptr`).
- Node pointing random to itself.
- Random pointers pointing to `nullptr`.

### Common Bugs to Avoid
- Failing to restore original list (must restore `curr->next = curr->next->next` to preserve caller's list invariant).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does curr->random->next point to the clone node?**  
  **A**: Because in Pass 1, we inserted the clone node directly after its original node. Thus, `curr->random` is the target original node, and its `.next` is unconditionally its cloned counterpart!


---

## 9. Tags & Related Problems

- **Tags**: `LinkedList`, `Pointers`, `Deep Copy`, `LeetCode-138`, `Hard`
- **Related problems to practice next**:
- **Clone Graph**: Graph cloning.
