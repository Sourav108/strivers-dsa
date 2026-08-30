# Hash Map vs Hash Set Time Complexity Breakdown (Step 1.6 — Learn Basic Hashing)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Hash Map vs Hash Set Time Complexity Breakdown](https://takeuforward.org/data-structure/hashing-basics/)
- **Difficulty**: Easy
- **Statement**: Analyze internal architectures, time/space complexities, collision vulnerabilities, and memory overheads of `std::map`, `std::unordered_map`, `std::set`, and `std::unordered_set` in C++.

---

## 1. Problem, Restated

Compare Red-Black Tree vs Hash Table container trade-offs under competitive programming constraints.

- **Input**: Parameters specified.
- **Output**: Value or side-effect meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

`std::map`/`set` use Red-Black Self-Balancing Binary Search Trees with strict $\mathcal{O}(\log N)$ worst-case bounds and ordered traversal. `std::unordered_map`/`set` use Hash Tables with $\mathcal{O}(1)$ average lookup, but degrade to $\mathcal{O}(N)$ under adversarial anti-hash test cases.

- **Underlying Pattern**: `Tree Map (RB-Tree) vs Unordered Hash Table Tradeoffs`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Using `unordered_map<long long, int>` on Codeforces without custom hash (vulnerable to anti-hash hack test cases causing $\mathcal{O}(N^2)$ TLE).

### C++17 Code
```cpp
#include <unordered_map>
using namespace std;
// Vulnerable to O(N^2) anti-hash hack attacks
unordered_map<long long, int> unsafeMap;
```

### Java Code
```java
import java.util.*;

class Solution {
    // Vulnerable to O(N^2) anti-hash hack attacks
    unordered_map<long, int> unsafeMap;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ average, but $\mathcal{O}(N)$ worst-case per query.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Standard `std::hash` is identity for integers.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Custom SplitMix64 Random Seed Hash for unhackable $\mathcal{O}(1)$ unordered maps.

### C++17 Code
```cpp
#include <iostream>
#include <chrono>
#include <unordered_map>
using namespace std;

// High-performance, anti-hash custom hash functor
struct custom_hash {
    static uint64_t splitmix64(uint64_t x) {
        x += 0x9e3779b97f4a7c15;
        x = (x ^ (x >> 30)) * 0xbf58476d1ce4e5b9;
        x = (x ^ (x >> 27)) * 0x94d049bb133111eb;
        return x ^ (x >> 31);
    }
    size_t operator()(uint64_t x) const {
        static const uint64_t FIXED_RANDOM = chrono::steady_clock::now().time_since_epoch().count();
        return splitmix64(x + FIXED_RANDOM);
    }
};

int main() {
    // Unhackable fast hash map
    unordered_map<long long, int, custom_hash> safeMap;
    safeMap[1000000000LL] = 1;
    cout << "Safe lookup: " << safeMap[1000000000LL] << "\n";
    return 0;
}
```

### Java Code
```java
import java.util.*;

// High-performance, anti-hash custom hash functor
static class custom_hash {
    static long splitmix64(long x) {
        x += 0x9e3779b97f4a7c15;
        x = (x ^ (x >> 30)) * 0xbf58476d1ce4e5b9;
        x = (x ^ (x >> 27)) * 0x94d049bb133111eb;
        return x ^ (x >> 31);
    }
    int operator()(long x) {
        static long FIXED_RANDOM = chrono::steady_clock::now().time_since_epoch().count();
        return splitmix64(x + FIXED_RANDOM);
    }
};

int main() {
    // Unhackable fast hash map
    unordered_map<long, int, custom_hash> safeMap;
    safeMap[1000000000LL] = 1;
    System.out.print("Safe lookup: " << safeMap[1000000000LL] << "\n");
    return 0;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ guaranteed average time.
- **Space Complexity**: $\mathcal{O}(N)$ memory.
- **Why this is optimal**: Randomized seed prevents predetermined hash collision attacks.

---

## 6. Dry Run

Container Comparison Matrix

| Step | Action / State Change | Result |
|---|---|---|
| ``std::map`` | Red-Black Tree | Search: $\mathcal{O}(\log N)$, Ordered keys, No hash attacks |
| ``std::unordered_map`` | Hash Table | Search: Avg $\mathcal{O}(1)$, Worst $\mathcal{O}(N)$, Unordered |
| ``std::set`` | Red-Black Tree | Search: $\mathcal{O}(\log N)$, Unique ordered keys |
| ``std::unordered_set`` | Hash Table | Search: Avg $\mathcal{O}(1)$, Unique unordered keys |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Adversarial tests with thousands of identical modulo bucket collisions.
- Large data scale requiring `reserve()`.

### Common Bugs to Avoid
- Relying on default `std::hash` for large integer keys in competitive programming.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why can standard unordered_map be hacked on Codeforces?**  
  **A**: The default hash implementation for integers in GCC is the identity function `f(x) = x`. Attackers construct inputs where all keys are multiples of the hash table bucket size, causing all $N$ keys to collide in a single bucket!


---

## 9. Tags & Related Problems

- **Tags**: `Hashing`, `Data Structures`, `Complexity`, `Easy`
- **Related problems to practice next**:
- **Collision Handling: Chaining**: Separate chaining details.
