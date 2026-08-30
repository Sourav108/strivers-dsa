# Division Rule & Folding Hash Functions (Step 1.6 — Learn Basic Hashing)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Division Rule & Folding Hash Functions](https://takeuforward.org/data-structure/hashing-theory/)
- **Difficulty**: Easy
- **Statement**: Explain how hash functions convert arbitrary keys into uniform array indices using the Division Method ($h(k) = k \% M$), Multiplication Method, Mid-Square Method, and Folding Method.

---

## 1. Problem, Restated

Understand mathematical hash function properties (Uniformity, Determinism, Avalanche effect, Prime table sizing).

- **Input**: Parameters specified.
- **Output**: Value or side-effect meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

A hash function maps universe $U$ to table size $M$. The **Division Method** $h(k) = k \pmod M$ works best when $M$ is a **Prime Number** not close to powers of 2 or 10. The **Folding Method** breaks large keys into digit chunks and sums them, ensuring all parts of the key influence the resulting hash.

- **Underlying Pattern**: `Hash Function Mathematical Design & Uniform Distribution`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Using power of 2 table size $M = 2^k$ with division method (ignores upper bits of key).

### C++17 Code
```cpp
// Bad: M = 8 ignores all bits except the last 3 bits!
int badHash(int key) {
    return key % 8; // Only depends on lowest 3 bits!
}
```

### Java Code
```java
class Solution {
    // Bad: M = 8 ignores all bits except the last 3 bits!
    int badHash(int key) {
        return key % 8; // Only depends on lowest 3 bits!
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Poor avalanche distribution.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Prime Modulo Division and Polynomial String Folding Hash.

### C++17 Code
```cpp
#include <iostream>
#include <string>
using namespace std;

// 1. Division Hash with Prime Modulus
int divisionHash(int key, int primeTableSize) {
    return (key % primeTableSize + primeTableSize) % primeTableSize;
}

// 2. Polynomial Rolling Hash for Strings (Folding Method)
long long polynomialStringHash(const string& s, int primeBase = 31, long long primeMod = 1e9 + 7) {
    long long hashVal = 0;
    long long pPower = 1;
    for (char ch : s) {
        hashVal = (hashVal + (ch - 'a' + 1) * pPower) % primeMod;
        pPower = (pPower * primeBase) % primeMod;
    }
    return hashVal;
}

int main() {
    cout << "Hash of 123456 (M=997): " << divisionHash(123456, 997) << "\n";
    cout << "Polynomial Hash of 'apple': " << polynomialStringHash("apple") << "\n";
    return 0;
}
```

### Java Code
```java
class Solution {
    // 1. Division Hash with Prime Modulus
    int divisionHash(int key, int primeTableSize) {
        return (key % primeTableSize + primeTableSize) % primeTableSize;
    }
    
    // 2. Polynomial Rolling Hash for Strings (Folding Method)
    long polynomialStringHash(String s, int primeBase = 31, long primeMod = 1e9 + 7) {
        long hashVal = 0;
        long pPower = 1;
        for (char ch : s) {
            hashVal = (hashVal + (ch - 'a' + 1) * pPower) % primeMod;
            pPower = (pPower * primeBase) % primeMod;
        }
        return hashVal;
    }
    
    int main() {
        System.out.print("Hash of 123456 (M=997): " << divisionHash(123456, 997) << "\n");
        System.out.print("Polynomial Hash of 'apple': " << polynomialStringHash("apple") << "\n");
        return 0;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ for integer keys, $\mathcal{O}(L)$ for strings of length $L$.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Prime moduli and polynomial folding maximize entropy and minimize clustering.

---

## 6. Dry Run

Division Hash of key $123456$ with $M = 997$

| Step | Action / State Change | Result |
|---|---|---|
| `Compute` | $123456 = 123 \times 997 + 825$ | Remainder = 825 |
| `Bucket Index` | 825 lies in $[0, 996]$ | Uniform mapping ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Negative integer keys (use `(key % M + M) % M`).

### Common Bugs to Avoid
- Choosing composite table sizes with common factors with the input keys.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why should hash table size M be a prime number in division hashing?**  
  **A**: If $M$ shares a common factor $d$ with keys, keys will only map to multiples of $d$, wasting $1 - 1/d$ of the hash table buckets. Prime numbers have no factors, guaranteeing uniform distribution across all $M$ buckets.


---

## 9. Tags & Related Problems

- **Tags**: `Hashing`, `Hash Functions`, `Maths`, `Easy`
- **Related problems to practice next**:
- **Collision Handling: Chaining**: Bucket storage.
