# Strings & Character Arrays in C++ (Step 1.1 — Things to Know in C++)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Strings & Character Arrays in C++](https://takeuforward.org/c/strings-in-c/)
- **Difficulty**: Easy
- **Statement**: Understand C-style strings (`char[]` with null-terminator `\0`), `std::string` class, Small String Optimization (SSO), string mutability, and standard manipulation functions.

---

## 1. Problem, Restated

Master character sequences, string concatenation, substrings, ASCII encoding, and memory management.

---

## 2. Intuition & Pattern

C-style strings are character arrays terminated by `\0`. `std::string` is a dynamic RAII wrapper that manages a heap/stack buffer, size, and capacity. Modern C++ standard libraries implement **Small String Optimization (SSO)**, storing strings up to 15–22 characters directly on the stack inside the string object without triggering any heap allocations.

- **Underlying Pattern**: `String Buffer Management & Small String Optimization (SSO)`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Repeated String Concatenation with + Operator in a Loop: Creates intermediate temporary copies in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
#include <iostream>
#include <string>
using namespace std;

// Inefficient: creates new string and copies all characters on every +=
string buildStringSlow(int n) {
    string s = "";
    for (int i = 0; i < n; i++) {
        s = s + to_string(i); // O(N^2) total copies
    }
    return s;
}
```

### Java Code
```java
class Solution {
    // Inefficient: creates new String and copies all characters on every +=
    String buildStringSlow(int n) {
        String s = "";
        for (int i = 0; i < n; i++) {
            s = s + String.valueOf(i); // O(N^2) total copies
        }
        return s;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time due to repeated reallocations and buffer copies.
- **Space Complexity**: $\mathcal{O}(N)$ memory.
- **Why it's not good enough**: `s = s + ...` allocates a new string buffer on every loop iteration.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Use `s += ...` with `s.reserve()` to amortize allocations in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
#include <iostream>
#include <string>
#include <algorithm>
using namespace std;

int main() {
    // std::string instantiation
    string s = "Hello, World!";
    
    // In-place mutation (O(1) access)
    s[0] = 'h';
    
    // Append in-place
    s += " C++17";
    
    // Common operations
    int len = s.length();
    string sub = s.substr(0, 5); // extracts "hello"
    
    // Reverse in-place
    reverse(s.begin(), s.end());
    
    cout << "Original reversed: " << s << "\n";
    cout << "Substring: " << sub << "\n";
    cout << "Length: " << len << "\n";
    
    return 0;
}
```

### Java Code
```java
class Solution {
    int main() {
        // String instantiation
        String s = "Hello, World!";
        
        // In-place mutation (O(1) access)
        s[0] = 'h';
        
        // Append in-place
        s += " C++17";
        
        // Common operations
        int len = s.length();
        String sub = s.substring(0, 0 + 5); // extracts "hello"
        
        // Reverse in-place
        reverse(s.begin(), s.end());
        
        System.out.print("Original reversed: " << s << "\n");
        System.out.print("Substring: " << sub << "\n");
        System.out.print("Length: " << len << "\n");
        
        return 0;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ linear time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Amortized buffer doubling and in-place operations avoid quadratic copying.

---

## 6. Dry Run & Key Reference

ASCII Character Arithmetic Trace

| Step | Action / State Change | Result |
|---|---|---|
| `'a' to 'z'` | ASCII 97 to 122 | 'c' - 'a' = 2 (0-based index) |
| `'A' to 'Z'` | ASCII 65 to 90 | 'C' - 'A' = 2 |
| `'0' to '9'` | ASCII 48 to 57 | '7' - '0' = 7 (integer value 7) |
| `'\0'` | ASCII 0 | Null terminator |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Empty string `s = ""` (size is 0, access `s[0]` is undefined).
- Reading strings containing spaces (use `getline(cin, s)`).
- String out-of-bounds access (`s[s.length()]` is null terminator, `s[s.length() + 1]` is invalid).

### Common Bugs to Avoid
- Modifying string literals: `char* str = "hello"; str[0] = 'H';` crashes with segmentation fault (string literals are in read-only text memory).
- Using `s = s + ch` inside loops instead of `s += ch` or `s.push_back(ch)`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is Small String Optimization (SSO)?**  
  **A**: SSO is an optimization where `std::string` uses its internal 24-byte or 32-byte struct memory to store short strings ($\le 15$ bytes) inline on the stack, bypassing heap allocation entirely.

- **Q2: Why is s += ch O(1) amortized while s = s + ch is O(N)?**  
  **A**: `s += ch` modifies the existing buffer in-place with exponential capacity doubling. `s = s + ch` creates a new temporary string, copies all $N$ characters, and assigns it back.

- **Q3: What is std::string_view in C++17?**  
  **A**: `std::string_view` is a non-owning read-only view of a character sequence (pointer + length) that allows $\mathcal{O}(1)$ substring slicing without allocating memory.


---

## 9. Tags & Related Problems

- **Tags**: `C++ Basics`, `Strings`, `Memory`, `Easy`
- **Related problems to practice next**:
- **Arrays & 2D Arrays Basics**: Character arrays.
- **Functions**: Passing strings by const reference.
