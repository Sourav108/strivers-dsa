# Bit Prerequisites for TRIE Problems (Bit Manipulation in 32-bit Tries) (Step 17.1 — Theory & Practice)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Bit Prerequisites for TRIE Problems (Bit Manipulation in 32-bit Tries)](https://takeuforward.org/trie/bit-prerequisites-for-trie-problems/)
- **Difficulty**: Easy
- **Statement**: Understand the essential bit manipulation concepts required for solving Advanced Bitwise Trie problems (such as Maximum XOR of Two Numbers and Maximum XOR Queries). Master checking the $i$-th bit, setting the $i$-th bit, flipping bits, and MSB-to-LSB binary tree representations.

---

## 1. Problem, Restated

Review and implement the four foundational bitwise operations (Check Bit, Set Bit, Clear Bit, and Bitwise Trie 2-Child Branching) essential for 31-bit integer Trie architectures in $\mathcal{O}(1)$ time.

- **Input**: Integer `num` and bit position `i`.
- **Output**: Transformed integer or bit value.
- **Complexity Goal**: $\mathcal{O}(1)$ time and space.

---

## 2. Intuition & Pattern

**The 4 Core Bitwise Operations for Binary Tries**: 
1) **Check if $i$-th bit is set**: 
   $$\text{bit} = (\text{num} \gg i) \,\&\, 1$$ 
   - If `1`, the $i$-th bit is ON (branch to `links[1]`). 
   - If `0`, the $i$-th bit is OFF (branch to `links[0]`). 
2) **Set the $i$-th bit**: 
   $$\text{num} = \text{num} \mid (1 \ll i)$$ 
3) **Clear the $i$-th bit**: 
   $$\text{num} = \text{num} \,\&\, \sim(1 \ll i)$$ 
4) **Toggle / Flip the $i$-th bit**: 
   $$\text{num} = \text{num} \oplus (1 \ll i)$$ 
**The 2-Way Binary Trie Node**: 
Instead of a 26-way alphabet array, a **Binary Trie Node** contains: 
- `Node* links[2]`: `links[0]` for bit 0, and `links[1]` for bit 1! 
- Every 32-bit non-negative integer is represented as a 31-step path from MSB (bit 31 or 30) down to LSB (bit 0) in $\mathcal{O}(32) = \mathcal{O}(1)$ time.

- **Underlying Pattern**: `Bit Manipulation Fundamentals for Binary Tries (`(num >> i) & 1`)`.

---

## 3. Approach 1 — Naive (String Binary Representation)

### Idea
Represent numbers as `std::string` of 32 binary characters `"0101..."` and use string Trie in $\mathcal{O}(32)$ with high string overhead.

### C++17 Code
```cpp
#include <string>
#include <bitset>
using namespace std;

string toBinaryString(int n) {
    return bitset<32>(n).to_string(); // Creates 32-byte string allocation
}
```

### Java Code
```java
class Solution {
    String toBinaryString(int n) {
        return bitset<32>(n).String.valueOf(); // Creates 32-byte String allocation
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(32)$ time with dynamic heap string allocations.
- **Space Complexity**: $\mathcal{O}(32)$ string memory per number.
- **Why it's not good enough**: Converting integers to strings introduces unnecessary memory allocations and string conversions.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard bitwise operations below directly achieve optimal $\mathcal{O}(1)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Hardware Bitwise Operations & Binary Trie)

### Idea
Direct Bitwise Bit Extraction `(num >> i) & 1` on 2-Way Pointer Trie in $\mathcal{O}(32) = \mathcal{O}(1)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <iostream>
#include <vector>
using namespace std;

// 2-Way Binary Trie Node for Bitwise Operations
struct BitNode {
    BitNode* links[2];
    
    BitNode() {
        links[0] = nullptr;
        links[1] = nullptr;
    }
    
    bool containsKey(int bit) {
        return links[bit] != nullptr;
    }
    
    BitNode* get(int bit) {
        return links[bit];
    }
    
    void put(int bit, BitNode* node) {
        links[bit] = node;
    }
};

class BitTriePrerequisites {
public:
    // 1. Check if the i-th bit of num is set (0-indexed from LSB)
    static int checkBit(int num, int i) {
        return (num >> i) & 1;
    }
    
    // 2. Set the i-th bit of num to 1
    static int setBit(int num, int i) {
        return num | (1 << i);
    }
    
    // 3. Clear the i-th bit of num to 0
    static int clearBit(int num, int i) {
        return num & ~(1 << i);
    }
    
    // 4. Toggle the i-th bit of num
    static int toggleBit(int num, int i) {
        return num ^ (1 << i);
    }
    
    // 5. Insert a 32-bit non-negative integer into a Binary Trie in O(32) = O(1)
    static void insertInteger(BitNode* root, int num) {
        BitNode* node = root;
        // Traverse from MSB (bit 31) down to LSB (bit 0)
        for (int i = 31; i >= 0; i--) {
            int bit = (num >> i) & 1;
            if (!node->containsKey(bit)) {
                node->put(bit, new BitNode());
            }
            node = node->get(bit);
        }
    }
};
```

### Java Code
```java
import java.util.*;

// 2-Way Binary Trie Node for Bitwise Operations
static class BitNode {
    BitNode* links[2];
    
    BitNode() {
        links[0] = null;
        links[1] = null;
    }
    
    boolean containsKey(int bit) {
        return links[bit] != null;
    }
    
    BitNode* get(int bit) {
        return links[bit];
    }
    
    void put(int bit, BitNode* node) {
        links[bit] = node;
    }
};

class BitTriePrerequisites {

    // 1. Check if the i-th bit of num is set (0-indexed from LSB)
    static int checkBit(int num, int i) {
        return (num >> i) & 1;
    }
    
    // 2. Set the i-th bit of num to 1
    static int setBit(int num, int i) {
        return num | (1 << i);
    }
    
    // 3. Clear the i-th bit of num to 0
    static int clearBit(int num, int i) {
        return num & ~(1 << i);
    }
    
    // 4. Toggle the i-th bit of num
    static int toggleBit(int num, int i) {
        return num ^ (1 << i);
    }
    
    // 5. Insert a 32-bit non-negative integer into a Binary Trie in O(32) = O(1)
    static void insertInteger(BitNode* root, int num) {
        BitNode* node = root;
        // Traverse from MSB (bit 31) down to LSB (bit 0)
        for (int i = 31; i >= 0; i--) {
            int bit = (num >> i) & 1;
            if (!node.containsKey(bit)) {
                node.put(bit, new BitNode());
            }
            node = node.get(bit);
        }
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ time per operation (fixed 32 bit-shifts).
- **Space Complexity**: $\mathcal{O}(1)$ space (at most 2 pointers per node).
- **Why this is optimal**: Bit-shift CPU instructions execute in single-cycle clock speed with zero heap copying overhead.

---

## 6. Dry Run

`num = 13` (Binary: `000...01101`), `i = 2`

| Step | Action / State Change | Result |
|---|---|---|
| `Check bit 2` | `13 >> 2` = 3 (binary `11`). `3 & 1 = 1` | Bit 2 is SET (1) |
| `Check bit 1` | `13 >> 1` = 6 (binary `110`). `6 & 1 = 0` | Bit 1 is UNSET (0) |
| `Set bit 1` | `13 | (1 << 1)` = `13 | 2 = 15` (binary `1111`) | Result = 15 |
| `Clear bit 2` | `13 & ~(1 << 2)` = `13 & ~4 = 13 & ...11011 = 9` (binary `1001`) | Result = 9 |
| `Toggle bit 0` | `13 ^ (1 << 0)` = `13 ^ 1 = 12` (binary `1100`) | Result = 12 |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $i = 31$ (sign bit on 32-bit signed integer; use `1U << 31` to prevent signed overflow).
- $i = 0$ (LSB).
- `num = 0`.

### Common Bugs to Avoid
- Using `1 << 31` with signed `int` (causes undefined behavior in C++ due to signed integer overflow; use `1LL << i` or `1U << i`).
- Confusing 0-indexed bit positions.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is `1LL << i` required instead of `1 << i` when dealing with 64-bit integers?**  
  **A**: In C++, literal integer `1` is a 32-bit signed integer. Shifting `1 << 35` causes undefined behavior / integer truncation! Using `1LL << 35` ensures a 64-bit `long long` literal.

- **Q2: Why do Bitwise Tries always traverse from MSB down to LSB?**  
  **A**: Because the highest bit (MSB) has the greatest mathematical weight ($2^{31} > 2^{30} + \dots + 2^0$). In Maximum XOR queries, matching opposite bits at higher positions produces strictly larger XOR values than any combination of lower bits!

- **Q3: How does a Bitwise Trie achieve O(1) time complexity?**  
  **A**: Because every 32-bit integer has a fixed depth of exactly 32 levels. The time to insert or query a number is $32 \times \mathcal{O}(1) = \mathcal{O}(1)$ independent of the number of elements in the array!


---

## 9. Tags & Related Problems

- **Tags**: `Trie`, `Bit Manipulation`, `Binary Trie`, `Bitwise Operations`, `Easy`
- **Related problems to practice next**:
- **Maximum XOR of Two Numbers**: Bitwise Trie application.
- **Maximum XOR Queries**: Offline Trie.
