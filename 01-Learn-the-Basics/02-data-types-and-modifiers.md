# Data Types & Modifiers in C++ (Step 1.1 — Things to Know in C++)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Data Types & Modifiers in C++](https://takeuforward.org/c/data-types-in-c/)
- **Difficulty**: Easy
- **Statement**: Understand primitive and user-defined data types in C++, their memory footprint, representation ranges, type modifiers (`signed`, `unsigned`, `short`, `long`, `const`), and type casting.

---

## 1. Problem, Restated

Master integer (`int`, `long long`), floating-point (`float`, `double`), character (`char`), and boolean (`bool`) memory layouts and range boundaries.

---

## 2. Intuition & Pattern

Data types define the bit width and interpretation of memory. `int` is 32-bit (range $\approx [-2.14 \times 10^9, 2.14 \times 10^9]$), while `long long` is 64-bit (range $\approx [-9.22 \times 10^{18}, 9.22 \times 10^{18}]$). Arithmetic operations on types that exceed their maximum bit-width cause undefined overflow in signed types and modular wrap-around in unsigned types.

- **Underlying Pattern**: `Memory Representation & Type Limits`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Using 32-bit `int` for all calculations without considering overflow bounds.

### C++17 Code
```cpp
#include <iostream>
using namespace std;

int main() {
    int a = 1000000;
    int b = 1000000;
    int c = a * b; // Signed 32-bit overflow: undefined behavior / negative result
    cout << c << "\n";
    return 0;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ memory.
- **Why it's not good enough**: 32-bit signed integer cannot store $10^{12} > 2.14 \times 10^9$.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Use appropriate 64-bit types (`long long`) and explicit type casting `(long long)a * b`.

### C++17 Code
```cpp
#include <iostream>
#include <climits>
#include <cstdint>
using namespace std;

int main() {
    int32_t a = 1000000;
    int32_t b = 1000000;
    
    // Explicit 64-bit cast prevents 32-bit intermediate multiplication overflow
    int64_t c = (int64_t)a * b; 
    
    char ch = 'A';
    double pi = 3.141592653589793;
    bool flag = true;
    
    cout << "64-bit Product: " << c << "\n";
    cout << "Size of int: " << sizeof(int) << " bytes\n";
    cout << "Size of long long: " << sizeof(long long) << " bytes\n";
    cout << "INT_MAX: " << INT_MAX << "\n";
    cout << "LLONG_MAX: " << LLONG_MAX << "\n";
    
    return 0;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ memory.
- **Why this is optimal**: Accurate memory allocation and zero overflow bugs.

---

## 6. Dry Run & Key Reference

Data Type Size & Range Table

| Step | Action / State Change | Result |
|---|---|---|
| ``char`` | 1 byte (8 bits) | -128 to 127 (or 0 to 255) |
| ``int`` | 4 bytes (32 bits) | -2,147,483,648 to 2,147,483,647 |
| ``long long`` | 8 bytes (64 bits) | -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807 |
| ``float`` | 4 bytes (32 bits) | $\approx 7$ decimal digits precision |
| ``double`` | 8 bytes (64 bits) | $\approx 15-17$ decimal digits precision |
| ``bool`` | 1 byte (8 bits) | `true` (1) or `false` (0) |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Integer overflow on product of two 32-bit numbers ($10^5 \times 10^5 = 10^{10} > \text{INT\_MAX}$).
- Floating point roundoff error in `double` when comparing equality (`a == b` vs `abs(a - b) < 1e-9`).
- `unsigned int` subtraction underflow ($0 - 1 = 4294967295$).

### Common Bugs to Avoid
- Writing `long long c = a * b;` when `a` and `b` are `int` (multiplication is computed as 32-bit first before widening). Fix: `(long long)a * b`.
- Using `float` for high-precision decimal calculations instead of `double`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is long long c = a * b still buggy if a and b are int?**  
  **A**: In C++, the right-hand side expression `a * b` is evaluated using the types of the operands (`int * int -> int`), which overflows BEFORE being assigned to the 64-bit variable `c`. You must write `(long long)a * b`.

- **Q2: Why does sizeof(bool) return 1 byte instead of 1 bit?**  
  **A**: Computer architectures address memory in units of bytes (8 bits), not individual bits. A CPU cannot directly address a single bit without masking.

- **Q3: What is the difference between signed and unsigned types?**  
  **A**: Signed uses Two's Complement where the most significant bit is the sign bit. Unsigned uses all bits for magnitude, doubling the positive range but allowing no negative values.

- **Q4: How does type promotion work in mixed expressions (e.g. int + double)?**  
  **A**: C++ promotes the narrower type to the wider type (`int` is promoted to `double`) to preserve precision during computation.

- **Q5: What are fixed-width integer types in <cstdint>?**  
  **A**: `int8_t`, `int16_t`, `int32_t`, `int64_t`, `uint64_t` guarantee exact bit-widths across all compilers and CPU architectures.


---

## 9. Tags & Related Problems

- **Tags**: `C++ Basics`, `Data Types`, `Memory`, `Easy`
- **Related problems to practice next**:
- **User Input / Output**: Streaming data types.
- **If-Else & Switch Statements**: Branching on types.
