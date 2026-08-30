# LFU Cache Implementation (Frequencies DLL Map) (Step 9.4 — Implementation Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [LFU Cache Implementation (Frequencies DLL Map)](https://takeuforward.org/data-structure/lfu-cache/)
- **Difficulty**: Hard
- **Statement**: Design a data structure that follows the constraints of a Least Frequently Used (LFU) cache supporting `get` and `put` in strict $\mathcal{O}(1)$ time. In case of a frequency tie, evict the least recently used key.

---

## 1. Problem, Restated

Implement LFU cache using two hash maps and multiple doubly linked lists grouped by frequency.

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Resulting vector of elements / integer answer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) `keyMap`: maps `key` to `Node(key, val, freq)`. 2) `freqMap`: maps `frequency` to a `DoublyLinkedList` of nodes with that frequency. 3) `minFreq`: tracks global minimum frequency. 4) When node frequency increases ($f \to f + 1$): remove from `freqList[f]`, add to `freqList[f+1]`. If `freqList[minFreq]` becomes empty, increment `minFreq++`. 5) On eviction: evict LRU node from `freqList[minFreq]` in $\mathcal{O}(1)$ time!

- **Underlying Pattern**: `Frequency Hash Map + Dual Doubly Linked Lists (`minFreq` tracking)`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Priority Queue (Heap) tracking frequencies in $\mathcal{O}(\log N)$ time per operation.

### C++17 Code
```cpp
// O(log N) Priority Queue LFU
```

### Java Code
```java
// Java equivalent
// O(log N) Priority Queue LFU
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log N)$ per operation.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Fails the $\mathcal{O}(1)$ strict time requirement.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — monotonic data structure below directly achieves optimal $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Dual Hash Map + Multi-DLL in strict $\mathcal{O}(1)$ time.

### C++17 Code
```cpp
#include <unordered_map>
using namespace std;

struct Node {
    int key, val, freq;
    Node *prev, *next;
    Node(int k, int v) : key(k), val(v), freq(1), prev(nullptr), next(nullptr) {}
};

struct DoublyLinkedList {
    Node *head, *tail;
    int size;
    DoublyLinkedList() {
        head = new Node(-1, -1);
        tail = new Node(-1, -1);
        head->next = tail;
        tail->prev = head;
        size = 0;
    }
    
    void addNode(Node* node) {
        Node* temp = head->next;
        node->next = temp;
        node->prev = head;
        head->next = node;
        temp->prev = node;
        size++;
    }
    
    void removeNode(Node* node) {
        Node* p = node->prev;
        Node* n = node->next;
        p->next = n;
        n->prev = p;
        size--;
    }
    
    Node* removeLRU() {
        if (size == 0) return nullptr;
        Node* lru = tail->prev;
        removeNode(lru);
        return lru;
    }
};

class LFUCache {
private:
    int capacity;
    int minFreq;
    int curSize;
    unordered_map<int, Node*> keyTable;
    unordered_map<int, DoublyLinkedList*> freqTable;
    
    void updateFreq(Node* node) {
        int oldFreq = node->freq;
        freqTable[oldFreq]->removeNode(node);
        
        if (oldFreq == minFreq && freqTable[oldFreq]->size == 0) {
            minFreq++;
        }
        
        node->freq++;
        if (freqTable.find(node->freq) == freqTable.end()) {
            freqTable[node->freq] = new DoublyLinkedList();
        }
        freqTable[node->freq]->addNode(node);
    }

public:
    LFUCache(int cap) : capacity(cap), minFreq(0), curSize(0) {}
    
    int get(int key) {
        if (keyTable.find(key) == keyTable.end()) return -1;
        Node* node = keyTable[key];
        updateFreq(node);
        return node->val;
    }
    
    void put(int key, int value) {
        if (capacity == 0) return;
        
        if (keyTable.find(key) != keyTable.end()) {
            Node* node = keyTable[key];
            node->val = value;
            updateFreq(node);
        } else {
            if (curSize == capacity) {
                Node* lru = freqTable[minFreq]->removeLRU();
                keyTable.erase(lru->key);
                delete lru;
                curSize--;
            }
            
            Node* newNode = new Node(key, value);
            minFreq = 1;
            if (freqTable.find(1) == freqTable.end()) {
                freqTable[1] = new DoublyLinkedList();
            }
            freqTable[1]->addNode(newNode);
            keyTable[key] = newNode;
            curSize++;
        }
    }
};
```

### Java Code
```java
import java.util.*;

static class Node {
    int key, val, freq;
    Node prev, next;
    public Node(int k, int v) { /* initialized: key(k), val(v), freq(1), prev(null), next(null)  */  }
};

static class DoublyLinkedList {
    Node head, tail;
    int size;
    DoublyLinkedList() {
        head = new Node(-1, -1);
        tail = new Node(-1, -1);
        head.next = tail;
        tail.prev = head;
        size = 0;
    }
    
    void addNode(Node  node) {
        Node  temp = head.next;
        node.next = temp;
        node.prev = head;
        head.next = node;
        temp.prev = node;
        size++;
    }
    
    void removeNode(Node  node) {
        Node  p = node.prev;
        Node  n = node.next;
        p.next = n;
        n.prev = p;
        size--;
    }
    
    Node  removeLRU() {
        if (size == 0) return null;
        Node  lru = tail.prev;
        removeNode(lru);
        return lru;
    }
};

class LFUCache {

    int capacity;
    int minFreq;
    int curSize;
    unordered_map<int, Node > keyTable;
    unordered_map<int, DoublyLinkedList*> freqTable;
    
    void updateFreq(Node  node) {
        int oldFreq = node.freq;
        freqTable[oldFreq].removeNode(node);
        
        if (oldFreq == minFreq && freqTable[oldFreq].size == 0) {
            minFreq++;
        }
        
        node.freq++;
        if (freqTable.find(node.freq) == freqTable.end()) {
            freqTable[node.freq] = new DoublyLinkedList();
        }
        freqTable[node.freq].addNode(node);
    }

    public LFUCache(int cap) { /* initialized: capacity(cap), minFreq(0), curSize(0)  */  }
    
    int get(int key) {
        if (keyTable.find(key) == keyTable.end()) return -1;
        Node  node = keyTable[key];
        updateFreq(node);
        return node.val;
    }
    
    void put(int key, int value) {
        if (capacity == 0) return;
        
        if (keyTable.find(key) != keyTable.end()) {
            Node  node = keyTable[key];
            node.val = value;
            updateFreq(node);
        } else {
            if (curSize == capacity) {
                Node  lru = freqTable[minFreq].removeLRU();
                keyTable.remove(lru.key);
                delete lru;
                curSize--;
            }
            
            Node  newNode = new Node(key, value);
            minFreq = 1;
            if (freqTable.find(1) == freqTable.end()) {
                freqTable[1] = new DoublyLinkedList();
            }
            freqTable[1].addNode(newNode);
            keyTable[key] = newNode;
            curSize++;
        }
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ strict time for both `get` and `put`.
- **Space Complexity**: $\mathcal{O}(\text{capacity})$ memory.
- **Why this is optimal**: Grouping nodes by frequency in individual DLLs allows $\mathcal{O}(1)$ frequency promotions and evictions.

---

## 6. Dry Run

LFU ($cap = 2$): `put(1, 1), put(2, 2), get(1), put(3, 3), get(2)`

| Step | Action / State Change | Result |
|---|---|---|
| `put(1,1), put(2,2)` | freq[1]: `[2] <-> [1]`, minFreq = 1 | curSize = 2 |
| `get(1)` | node 1 freq becomes 2 -> freq[2]: `[1]`, freq[1]: `[2]`, minFreq = 1 | returns 1 |
| `put(3,3)` | evicts LRU from freq[minFreq=1] (node 2) -> adds 3 at freq 1 | evicted 2 |
| `get(2)` | not in cache | returns -1 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $capacity = 0$ (put ignored immediately).
- All elements have same frequency (falls back to LRU).

### Common Bugs to Avoid
- Not resetting `minFreq = 1` when a brand new key is inserted.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does minFreq only ever increment by 1 during updateFreq?**  
  **A**: Because an existing node's frequency only increases by $+1$ on a single access. If the old frequency bucket was `minFreq` and is now empty, the new minimum frequency MUST be $minFreq + 1$!


---

## 9. Tags & Related Problems

- **Tags**: `Design`, `LFU Cache`, `Doubly LinkedList`, `LeetCode-460`, `Hard`
- **Related problems to practice next**:
- **LRU Cache**: Recency cache.
