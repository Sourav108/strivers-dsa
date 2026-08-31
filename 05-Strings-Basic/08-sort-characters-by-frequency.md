# Sort Characters by Frequency (Step 5.2 — Medium String Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Sort Characters by Frequency](https://takeuforward.org/strings/sort-characters-by-frequency/)
- **Difficulty**: Medium
- **Statement**: Given a string $s$, sort it in decreasing order based on the frequency of the characters. The frequency of a character is the number of times it appears in the string.

---

## 1. Problem, Restated

Order characters by descending frequency count, expanding each character by its multiplicity.

- **Input**: Problem parameters.
- **Output**: Transformed string or calculated integer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) Count frequencies with hash map. 2) Use **Bucket Sort** where `buckets[freq]` stores all characters appearing `freq` times. Since max frequency $\le N$, iterating buckets backwards from $N$ down to $1$ constructs the result in $\mathcal{O}(N)$ time!

- **Underlying Pattern**: `Bucket Sort / Max-Heap on Frequency Map`.

---

## 3. Approach 1 — Brute Force / Naive

### Idea
Sort pair list with custom comparator in $\mathcal{O}(K \log K)$ where $K \le 128$.

### C++17 Code
```cpp
#include <string>
#include <vector>
#include <unordered_map>
#include <algorithm>
using namespace std;
string frequencySortHeap(string s) {
    unordered_map<char, int> freq;
    for (char c : s) freq[c]++;
    vector<pair<int, char>> v;
    for (auto& [c, count] : freq) v.push_back({count, c});
    sort(v.rbegin(), v.rend());
    string ans = "";
    for (auto& [count, c] : v) ans += string(count, c);
    return ans;
}
```

### Java Code
```java
import java.util.*;

class Solution {
    String frequencySortHeap(String s) {
        Map<Character, Integer> freq = new HashMap<>();
        for (char c : s) freq[c]++;
        List<int[]> v;
        for (var [c, count] : freq) v.add({count, c});
        sort(v.rbegin(), v.rend());
        String ans = "";
        for (var [count, c] : v) ans += String(count, c);
        return ans;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N + K \log K)$ time.
- **Space Complexity**: $\mathcal{O}(K)$ space.
- **Why it's not good enough**: Uses comparison sorting.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Bucket Sort by Frequency in linear $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
#include <string>
#include <vector>
#include <unordered_map>
using namespace std;

class Solution {
public:
    string frequencySort(string s) {
        int n = s.length();
        unordered_map<char, int> freq;
        for (char c : s) freq[c]++;
        
        // Bucket array where index is frequency (0 to n)
        vector<string> buckets(n + 1, "");
        for (const auto& [ch, count] : freq) {
            buckets[count].append(count, ch);
        }
        
        string result = "";
        result.reserve(n);
        
        // Scan buckets from highest frequency to lowest
        for (int f = n; f >= 1; f--) {
            if (!buckets[f].empty()) {
                result += buckets[f];
            }
        }
        
        return result;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    String frequencySort(String s) {
        int n = s.length();
        Map<Character, Integer> freq = new HashMap<>();
        for (char c : s) freq[c]++;
        
        // Bucket array where index is frequency (0 to n)
        String[] buckets(n + 1, "");
        for (var [ch, count] : freq) {
            buckets[count].append(count, ch);
        }
        
        String result = "";
        result.reserve(n);
        
        // Scan buckets from highest frequency to lowest
        for (int f = n; f >= 1; f--) {
            if (!buckets[f].isEmpty()) {
                result += buckets[f];
            }
        }
        
        return result;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ linear time.
- **Space Complexity**: $\mathcal{O}(N)$ bucket array.
- **Why this is optimal**: Bucket sort eliminates logarithmic sorting overhead.

---

## 6. Dry Run

$s = \text{"tree"}$

| Step | Action / State Change | Result |
|---|---|---|
| `Freq Map` | e: 2, t: 1, r: 1 | Max freq = 2 |
| `Buckets` | bucket[2] = "ee", bucket[1] = "tr" | Built in O(N) |
| `Output` | bucket[2] + bucket[1] | `"eetr"` (or `"eert"`) ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- All characters same `"aaaa"`.
- All unique `"abcd"`.
- Case sensitive ('A' != 'a').

### Common Bugs to Avoid
- Forgetting that max frequency cannot exceed $N$.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is Bucket Sort linear time here?**  
  **A**: Because frequencies are bounded by $N$ (string length). An array of buckets from 1 to $N$ sorts in $\mathcal{O}(N)$ time without comparisons.


---

## 9. Tags & Related Problems

- **Tags**: `Strings`, `Hashing`, `Bucket Sort`, `LeetCode-451`, `Medium`
- **Related problems to practice next**:
- **Top K Frequent Elements**: Heap/Bucket selection.
