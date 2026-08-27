# Maximum XOR of Two Numbers in an Array (Bitwise Trie Search) (Step 17.1 — Theory & Practice)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Maximum XOR of Two Numbers in an Array (Bitwise Trie Search)](https://takeuforward.org/data-structure/maximum-xor-of-two-numbers-in-an-array/)
- **Difficulty**: Medium
- **Statement**: Given an integer array `nums`, return the maximum result of `nums[i] XOR nums[j]`, where $0 \le i \le j < n$.

---

## 1. Problem, Restated

Find two numbers in `nums` whose bitwise XOR is maximized using a 32-bit Binary Trie with greedy opposite-bit traversal in $\mathcal{O}(N \times 32) = \mathcal{O}(N)$ time and $\mathcal{O}(N \times 32)$ space.

- **Input**: Array of integers `nums`.
- **Output**: Maximum bitwise XOR value.
- **Complexity Goal**: $\mathcal{O}(N)$ time and space.

---

## 2. Intuition & Pattern

**The Greedy Opposite Bit Invariant**: 
For any number $X$, to maximize $X \oplus Y$: 
- At each bit position $i$ from MSB (bit 31) down to LSB (bit 0): 
  - If the $i$-th bit of $X$ is $b$, we WANT the $i$-th bit of $Y$ to be the **OPPOSITE bit $(1 - b)$** (since $1 \oplus 0 = 1$ and $0 \oplus 1 = 1$, setting the $i$-th bit of the XOR result to 1). 
  - If the Trie has a child node with bit $(1 - b)$, we greedily move to $(1 - b)$ and set the $i$-th bit of the result: `maxXOR |= (1 << i)`. 
  - If the opposite bit is not available, we have no choice but to follow the same bit $b$. 
**Algorithm**: 
1) Build a Binary Trie by inserting all numbers in `nums` in $\mathcal{O}(N \times 32)$. 
2) For each number $X \in \text{nums}$, query the Trie to find the companion number $Y$ maximizing $X \oplus Y$ in $\mathcal{O}(32)$. 
3) Global answer is $\max_{X}(X \oplus \text{findMax}(X))$. Runs in strictly linear $\mathcal{O}(N)$ time!

- **Underlying Pattern**: `Binary Trie with Greedy Opposite-Bit Path Search (`1 - bit`)`.

---

## 3. Approach 1 — Naive (All Pairs XOR)

### Idea
Nested loops checking all $N(N-1)/2$ pairs in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class SolutionNaive {
public:
    int findMaximumXOR(vector<int>& nums) {
        int maxVal = 0, n = nums.size();
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                maxVal = max(maxVal, nums[i] ^ nums[j]);
            }
        }
        return maxVal;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ space.
- **Why it's not good enough**: Quadratic pair comparisons cause TLE for $N = 2 \times 10^5$.

---

## 4. Approach 2 — Better (Bitwise Prefix Hash Set)

### Idea
Bitwise Prefix Hash Set with Candidate Testing in O(32 * N) time and O(N) space.

### C++17 Code
```cpp
#include <vector>
#include <unordered_set>
#include <algorithm>
using namespace std;

class SolutionHashSet {
public:
    int findMaximumXOR(vector<int>& nums) {
        int maxXOR = 0, mask = 0;
        for (int i = 31; i >= 0; i--) {
            mask |= (1 << i);
            unordered_set<int> prefixes;
            for (int n : nums) prefixes.insert(n & mask);
            int candidate = maxXOR | (1 << i);
            for (int p : prefixes) {
                if (prefixes.count(p ^ candidate)) {
                    maxXOR = candidate;
                    break;
                }
            }
        }
        return maxXOR;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(32 \times N)$ time with hash set re-hashing overhead.
- **Space Complexity**: $\mathcal{O}(N)$ auxiliary hash set space.
- **Why it's still not optimal**: 32 hash set allocations create cache misses and bucket collision overhead compared to a direct pointer Trie.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Binary Trie Greedy Bit Search)

### Idea
2-Way Binary Trie with Greedy Bit Search in $\mathcal{O}(32 \times N) = \mathcal{O}(N)$ time and $\mathcal{O}(32 \times N)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

// 2-Way Binary Trie Node
struct Node {
    Node* links[2];
    
    Node() {
        links[0] = nullptr;
        links[1] = nullptr;
    }
    
    bool containsKey(int bit) {
        return links[bit] != nullptr;
    }
    
    Node* get(int bit) {
        return links[bit];
    }
    
    void put(int bit, Node* node) {
        links[bit] = node;
    }
};

class Trie {
private:
    Node* root;
    
public:
    Trie() {
        root = new Node();
    }
    
    // Inserts a number into the Trie in O(32) time
    void insert(int num) {
        Node* node = root;
        for (int i = 31; i >= 0; i--) {
            int bit = (num >> i) & 1;
            if (!node->containsKey(bit)) {
                node->put(bit, new Node());
            }
            node = node->get(bit);
        }
    }
    
    // Finds maximum XOR value for a given number in O(32) time
    int getMax(int num) {
        Node* node = root;
        int maxNum = 0;
        
        for (int i = 31; i >= 0; i--) {
            int bit = (num >> i) & 1;
            int oppositeBit = 1 - bit;
            
            // Greedily choose opposite bit to make the i-th bit in XOR result 1
            if (node->containsKey(oppositeBit)) {
                maxNum |= (1 << i);
                node = node->get(oppositeBit);
            } else {
                // Must take the same bit (i-th bit in XOR becomes 0)
                node = node->get(bit);
            }
        }
        
        return maxNum;
    }
};

class Solution {
public:
    int findMaximumXOR(vector<int>& nums) {
        Trie trie;
        
        // Step 1: Insert all numbers into the Binary Trie
        for (int x : nums) {
            trie.insert(x);
        }
        
        // Step 2: Query max XOR for each number
        int maxXOR = 0;
        for (int x : nums) {
            maxXOR = max(maxXOR, trie.getMax(x));
        }
        
        return maxXOR;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(32 \times N) = \mathcal{O}(N)$ time (insertion takes $32N$ steps, querying takes $32N$ steps).
- **Space Complexity**: $\mathcal{O}(32 \times N)$ space (at most $32 \times N$ nodes in Binary Trie).
- **Why this is optimal**: MSB-first greedy bitwise choice guarantees finding the mathematically optimal pair at every bit position.

---

## 6. Dry Run

`nums = [3, 10, 5, 25, 2, 8]` ($N = 6$)

| Step | Action / State Change | Result |
|---|---|---|
| `Binary of 25` | `25 = 00011001` (bit 4 is set) | Inserted in Trie |
| `Query on 5` | `5 = 00000101`. At bit 4: 5 has 0, opposite is 1. Trie has 25 (bit 4 = 1)! $\implies$ branch to 1 | Bit 4 of XOR = 1 |
| `At bit 3` | 5 has 0, opposite is 1. Trie node from 25 has 1! $\implies$ branch to 1 | Bit 3 of XOR = 1 |
| `At bit 2` | 5 has 1, opposite is 0. 25 has 0! $\implies$ branch to 0 | Bit 2 of XOR = 1 |
| `At bit 1` | 5 has 0, opposite is 1. 25 has 0 (same bit) | Bit 1 of XOR = 0 |
| `At bit 0` | 5 has 1, opposite is 0. 25 has 1 (same bit) | Bit 0 of XOR = 0 |
| `Result` | $5 \oplus 25 = 28$ (`00011100` = 28) | Max XOR = 28 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 1$ (returns 0: $x \oplus x = 0$).
- All numbers equal (returns 0).
- Numbers up to $2^{31} - 1$.

### Common Bugs to Avoid
- Using signed bit-shift `(1 << 31)` with signed integers (leads to undefined behavior; use `(1U << i)` or `(1LL << i)`).
- Iterating from LSB to MSB (MSB MUST be prioritized because $2^k > \sum_{j=0}^{k-1} 2^j$).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does the greedy MSB-first choice mathematically guarantee the global maximum?**  
  **A**: Because $2^k > \sum_{j=0}^{k-1} 2^j$. Having a `1` at the $k$-th bit is strictly greater than having `1`s at all lower bit positions $0 \dots k-1$ combined! Therefore, securing `1` at the highest possible bit takes absolute priority!

- **Q2: How does this problem extend to Maximum XOR with an Element from Array (Problem 07)?**  
  **A**: In Problem 07, queries have an upper-bound constraint $A_i \le M_i$. We sort the array and queries, dynamically inserting only elements $\le M_i$ before answering each query offline!

- **Q3: What is the maximum number of nodes a Binary Trie of N 32-bit integers can have?**  
  **A**: At most $1 + 32 \times N$ nodes. In practice, prefix bit sharing reduces memory by $60-80\%$.


---

## 9. Tags & Related Problems

- **Tags**: `Trie`, `Bit Manipulation`, `Binary Trie`, `Bitwise XOR`, `LeetCode-421`, `Medium`
- **Related problems to practice next**:
- **Maximum XOR Queries**: Offline query constraint.
- **Bit Prerequisites for Trie**: Fundamentals.
