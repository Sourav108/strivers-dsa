# LRU Cache Implementation (Hash Map + Doubly LL) (Step 9.4 — Implementation Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [LRU Cache Implementation (Hash Map + Doubly LL)](https://takeuforward.org/data-structure/lru-cache-implementation/)
- **Difficulty**: Hard
- **Statement**: Design a data structure that follows the constraints of a Least Recently Used (LRU) cache supporting `get(key)` and `put(key, value)` in strict $\mathcal{O}(1)$ time.

---

## 1. Problem, Restated

Implement LRU Cache using Hash Map + Doubly Linked List with dummy head and tail.

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Resulting vector of elements / integer answer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) **Hash Map**: `unordered_map<int, Node*>` maps `key` to its DLL Node pointer for $\mathcal{O}(1)$ lookup. 2) **Doubly Linked List**: Maintains recency order. Most Recently Used (MRU) after `head`, Least Recently Used (LRU) before `tail`. 3) `get(key)`: If key exists, move node to MRU (right after `head`) and return value. 4) `put(key, value)`: If exists, update value and move to MRU. If new, insert right after `head`. If size exceeds capacity, evict LRU node right before `tail` (`tail->prev`).

- **Underlying Pattern**: `Hash Map + Doubly Linked List with Sentinel Dummy Nodes`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Vector of pairs shifting elements on access in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
// O(N) array search LRU
```

### Java Code
```java
// Java equivalent
// O(N) array search LRU
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ per get/put.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Linear vector search.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — monotonic data structure below directly achieves optimal $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Hash Map + Doubly Linked List with Dummy Sentinel Nodes in $\mathcal{O}(1)$ time.

### C++17 Code
```cpp
#include <unordered_map>
using namespace std;

class LRUCache {
private:
    struct Node {
        int key;
        int val;
        Node* prev;
        Node* next;
        Node(int k, int v) : key(k), val(v), prev(nullptr), next(nullptr) {}
    };
    
    int capacity;
    unordered_map<int, Node*> mp;
    Node* head;
    Node* tail;
    
    void addNode(Node* newNode) {
        Node* temp = head->next;
        newNode->next = temp;
        newNode->prev = head;
        head->next = newNode;
        temp->prev = newNode;
    }
    
    void deleteNode(Node* delNode) {
        Node* delPrev = delNode->prev;
        Node* delNext = delNode->next;
        delPrev->next = delNext;
        delNext->prev = delPrev;
    }

public:
    LRUCache(int cap) : capacity(cap) {
        head = new Node(-1, -1);
        tail = new Node(-1, -1);
        head->next = tail;
        tail->prev = head;
    }
    
    ~LRUCache() {
        Node* curr = head;
        while (curr) {
            Node* nextNode = curr->next;
            delete curr;
            curr = nextNode;
        }
    }
    
    int get(int key) {
        if (mp.find(key) == mp.end()) return -1;
        
        Node* resNode = mp[key];
        deleteNode(resNode);
        addNode(resNode); // move to MRU (head)
        return resNode->val;
    }
    
    void put(int key, int value) {
        if (mp.find(key) != mp.end()) {
            Node* existing = mp[key];
            existing->val = value;
            deleteNode(existing);
            addNode(existing);
        } else {
            if ((int)mp.size() == capacity) {
                // Evict LRU node (node before tail)
                Node* lru = tail->prev;
                mp.erase(lru->key);
                deleteNode(lru);
                delete lru;
            }
            Node* newNode = new Node(key, value);
            addNode(newNode);
            mp[key] = newNode;
        }
    }
};
```

### Java Code
```java
class LRUCache {

    static class Node {
        int key;
        int val;
        Node  prev;
        Node  next;
        public Node(int k, int v) { /* initialized: key(k), val(v), prev(null), next(null)  */  }
    };
    
    int capacity;
    unordered_map<int, Node > mp;
    Node  head;
    Node  tail;
    
    void addNode(Node  newNode) {
        Node  temp = head.next;
        newNode.next = temp;
        newNode.prev = head;
        head.next = newNode;
        temp.prev = newNode;
    }
    
    void deleteNode(Node  delNode) {
        Node  delPrev = delNode.prev;
        Node  delNext = delNode.next;
        delPrev.next = delNext;
        delNext.prev = delPrev;
    }

    public LRUCache(int cap) { /* initialized: capacity(cap)  */ 
        head = new Node(-1, -1);
        tail = new Node(-1, -1);
        head.next = tail;
        tail.prev = head;
     }
    
    ~LRUCache() {
        Node  curr = head;
        while (curr) {
            Node  nextNode = curr.next;
            delete curr;
            curr = nextNode;
        }
    }
    
    int get(int key) {
        if (mp.find(key) == mp.end()) return -1;
        
        Node  resNode = mp[key];
        deleteNode(resNode);
        addNode(resNode); // move to MRU (head)
        return resNode.val;
    }
    
    void put(int key, int value) {
        if (mp.find(key) != mp.end()) {
            Node  existing = mp[key];
            existing.val = value;
            deleteNode(existing);
            addNode(existing);
        } else {
            if (mp.length == capacity) {
                // Evict LRU node (node before tail)
                Node  lru = tail.prev;
                mp.remove(lru.key);
                deleteNode(lru);
                delete lru;
            }
            Node  newNode = new Node(key, value);
            addNode(newNode);
            mp[key] = newNode;
        }
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ strict time for both `get` and `put`.
- **Space Complexity**: $\mathcal{O}(\text{capacity})$ for hash map and DLL nodes.
- **Why this is optimal**: Hash map provides $\mathcal{O}(1)$ lookup, DLL pointer manipulation provides $\mathcal{O}(1)$ insertion/deletion.

---

## 6. Dry Run

LRU ($cap = 2$): `put(1, 1), put(2, 2), get(1), put(3, 3), get(2)`

| Step | Action / State Change | Result |
|---|---|---|
| `put(1, 1), put(2, 2)` | head <-> [2] <-> [1] <-> tail | mp: {1, 2} |
| `get(1)` | moves [1] to MRU -> head <-> [1] <-> [2] <-> tail | returns 1 |
| `put(3, 3)` | evicts [2] (tail->prev) -> head <-> [3] <-> [1] <-> tail | mp: {1, 3} |
| `get(2)` | not in mp | returns -1 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Capacity 1.
- Updating value of existing key.

### Common Bugs to Avoid
- Erasing from DLL without erasing `lru->key` from hash map.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why are dummy head and tail nodes essential?**  
  **A**: Dummy sentinels eliminate edge-case null-pointer checks for empty list, head insertion, and tail deletion, making all pointer reconnections unified and bug-free.


---

## 9. Tags & Related Problems

- **Tags**: `Design`, `Hash Map`, `Doubly LinkedList`, `LeetCode-146`, `Hard`
- **Related problems to practice next**:
- **LFU Cache**: Frequency based cache.
