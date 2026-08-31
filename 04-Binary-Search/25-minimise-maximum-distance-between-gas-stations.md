# Minimise Maximum Distance between Gas Stations (Step 4.2 — BS on Answers)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Minimise Maximum Distance between Gas Stations](https://takeuforward.org/binary-search/minimise-maximum-distance-between-gas-stations/)
- **Difficulty**: Hard
- **Statement**: You are given a sorted array `stations` representing positions of gas stations along a highway, and an integer $k$. You must add $k$ new gas stations anywhere on the highway such that the maximum distance between adjacent gas stations is minimized. Return the smallest possible maximum distance (accurate to $10^{-6}$).

---

## 1. Problem, Restated

Add $k$ new stations into the existing gaps between stations such that the maximum resulting gap length between any two adjacent stations is as small as possible.

- **Input**: Input arrays/parameters.
- **Output**: Value meeting the specification.
- **Constraints**: $1 \le n, m \le 10^5$, standard 32/64-bit limits.

---

## 2. Intuition & Pattern

Suppose we want every adjacent gap between gas stations to be at most distance $D$ (a floating-point number). For an existing gap of length $\Delta = stations[i] - stations[i-1]$, the number of new stations needed to reduce this gap so that no sub-gap exceeds $D$ is $\lfloor \Delta / D \rfloor$. Summing this across all gaps gives the total new stations needed. As $D$ increases, stations needed monotonically decreases. We can Binary Search on real numbers `double` with stopping condition `high - low > 1e-6`.

- **Underlying Pattern**: `Floating-Point Binary Search on Continuous Answer Space`.
- **The "Aha!" Moment**: Recognizing the monotonic boundary or partition cut that enables logarithmic halving.

---

## 3. Approach 1 — Brute Force

### Idea
Greedy Iteration (Pick Maximum Gap): Repeat $k$ times: find the gap currently having the largest sub-section length, and add 1 station to it.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

double minimiseMaxDistanceBrute(vector<int>& stations, int k) {
    int n = stations.size();
    vector<int> howMany(n - 1, 0); // stations placed in each gap
    
    for (int gas = 1; gas <= k; gas++) {
        double maxSection = -1;
        int maxIdx = -1;
        for (int i = 0; i < n - 1; i++) {
            double diff = stations[i + 1] - stations[i];
            double sectionLen = diff / (double)(howMany[i] + 1);
            if (sectionLen > maxSection) {
                maxSection = sectionLen;
                maxIdx = i;
            }
        }
        howMany[maxIdx]++;
    }
    
    double maxAns = -1;
    for (int i = 0; i < n - 1; i++) {
        double diff = stations[i + 1] - stations[i];
        double sectionLen = diff / (double)(howMany[i] + 1);
        maxAns = max(maxAns, sectionLen);
    }
    return maxAns;
}
```

### Java Code
```java
class Solution {
    double minimiseMaxDistanceBrute(int[] stations, int k) {
        int n = stations.length;
        int[] howMany = new int[n - 1]; // stations placed in each gap
        
        for (int gas = 1; gas <= k; gas++) {
            double maxSection = -1;
            int maxIdx = -1;
            for (int i = 0; i < n - 1; i++) {
                double diff = stations[i + 1] - stations[i];
                double sectionLen = diff / (double)(howMany[i] + 1);
                if (sectionLen > maxSection) {
                    maxSection = sectionLen;
                    maxIdx = i;
                }
            }
            howMany[maxIdx]++;
        }
        
        double maxAns = -1;
        for (int i = 0; i < n - 1; i++) {
            double diff = stations[i + 1] - stations[i];
            double sectionLen = diff / (double)(howMany[i] + 1);
            maxAns = Math.max(maxAns, sectionLen);
        }
        return maxAns;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(k \cdot n)$ — for each of the $k$ stations, scans all $n-1$ gaps. For $k = 10^6, n = 10^5$, operations $= 10^{11} \implies$ TLE.
- **Space Complexity**: $\mathcal{O}(n)$ auxiliary space.
- **Why it's not good enough**: Scanning all gaps linearly for every single added station is too slow.

---

## 4. Approach 2 — Better

### Idea
Priority Queue (Max-Heap): Store each gap in a max-heap keyed by current section length. Pop the largest gap, insert a station into it, update its section length, and push back into the heap.

### C++17 Code
```cpp
#include <vector>
#include <queue>
using namespace std;

double minimiseMaxDistanceHeap(vector<int>& stations, int k) {
    int n = stations.size();
    vector<int> howMany(n - 1, 0);
    // Max-heap storing {section_length, gap_index}
    priority_queue<pair<double, int>> pq;
    
    for (int i = 0; i < n - 1; i++) {
        pq.push({(double)(stations[i + 1] - stations[i]), i});
    }
    
    for (int gas = 1; gas <= k; gas++) {
        auto top = pq.top();
        pq.pop();
        int secIdx = top.second;
        
        howMany[secIdx]++;
        double initialDiff = stations[secIdx + 1] - stations[secIdx];
        double newSectionLen = initialDiff / (double)(howMany[secIdx] + 1);
        pq.push({newSectionLen, secIdx});
    }
    
    return pq.top().first;
}
```

### Java Code
```java
import java.util.*;

class Solution {
    double minimiseMaxDistanceHeap(int[] stations, int k) {
        int n = stations.length;
        int[] howMany = new int[n - 1];
        // Max-heap storing {section_length, gap_index}
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) . Integer.compare(a[0], b[0]));
        
        for (int i = 0; i < n - 1; i++) {
            pq.push({(double)(stations[i + 1] - stations[i]), i});
        }
        
        for (int gas = 1; gas <= k; gas++) {
            var top = pq.peek();
            pq.pop();
            int secIdx = top.second;
            
            howMany[secIdx]++;
            double initialDiff = stations[secIdx + 1] - stations[secIdx];
            double newSectionLen = initialDiff / (double)(howMany[secIdx] + 1);
            pq.push({newSectionLen, secIdx});
        }
        
        return pq.peek().first;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n \log n + k \log n)$ — heap operations take $\mathcal{O}(\log n)$ per added station. For $k = 10^6$, takes $\approx 1.7 \times 10^7$ ops.
- **Space Complexity**: $\mathcal{O}(n)$ for priority queue.
- **Why it's still not optimal**: Time complexity depends directly on $k$. When $k = 10^9$, Heap approach TLEs; Binary Search runs in $\mathcal{O}(n \log(\text{range}/\epsilon))$ independently of $k$.

---

## 5. Approach 3 — Optimal

### Idea
Binary Search on Real Numbers: `low = 0.0, high = max(gap)`. While `high - low > 1e-6`, compute `mid = (low + high) / 2.0`. Calculate `stationsNeeded(mid) = sum(floor((stations[i] - stations[i-1]) / mid))`. If `stationsNeeded <= k`, `high = mid`; else `low = mid`. Return `high`.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
private:
    int numberOfGasStationsRequired(double dist, const vector<int>& stations) {
        int count = 0;
        for (size_t i = 1; i < stations.size(); i++) {
            double gap = stations[i] - stations[i - 1];
            int numberInBetween = (int)(gap / dist);
            
            // If gap is exactly divisible by dist, subtract 1 (station already at boundary)
            if (gap == numberInBetween * dist) {
                numberInBetween--;
            }
            count += numberInBetween;
        }
        return count;
    }

public:
    double findSmallestMaxDist(vector<int>& stations, int k) {
        int n = stations.size();
        double low = 0.0;
        double high = 0.0;
        
        // Maximum initial gap is our search space upper bound
        for (int i = 0; i < n - 1; i++) {
            high = max(high, (double)(stations[i + 1] - stations[i]));
        }
        
        // Precision threshold 1e-6
        double diff = 1e-6;
        while (high - low > diff) {
            double mid = low + (high - low) / 2.0;
            int count = numberOfGasStationsRequired(mid, stations);
            
            if (count <= k) {
                high = mid; // feasible gap distance, try to minimize further
            } else {
                low = mid;  // too many stations needed, distance too small
            }
        }
        
        return high;
    }
};
```

### Java Code
```java
class Solution {

    int numberOfGasStationsRequired(double dist, int[] stations) {
        int count = 0;
        for (int i = 1; i < stations.length; i++) {
            double gap = stations[i] - stations[i - 1];
            int numberInBetween = (int)(gap / dist);
            
            // If gap is exactly divisible by dist, subtract 1 (station already at boundary)
            if (gap == numberInBetween * dist) {
                numberInBetween--;
            }
            count += numberInBetween;
        }
        return count;
    }

    double findSmallestMaxDist(int[] stations, int k) {
        int n = stations.length;
        double low = 0.0;
        double high = 0.0;
        
        // Maximum initial gap is our search space upper bound
        for (int i = 0; i < n - 1; i++) {
            high = Math.max(high, (double)(stations[i + 1] - stations[i]));
        }
        
        // Precision threshold 1e-6
        double diff = 1e-6;
        while (high - low > diff) {
            double mid = low + (high - low) / 2.0;
            int count = numberOfGasStationsRequired(mid, stations);
            
            if (count <= k) {
                high = mid; // feasible gap distance, try to minimize further
            } else {
                low = mid;  // too many stations needed, distance too small
            }
        }
        
        return high;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n \log_2(\frac{\text{maxDist}}{10^{-6}}))$ — for maxDist $= 10^9$ and $\epsilon = 10^{-6}$, number of iterations $\approx \log_2(10^{15}) \approx 50$. Total operations: $50 \times n$, running in $\approx 15\text{ms}$ independently of $k$.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Eliminates $k$-dependency completely, running in pure $\mathcal{O}(n)$ space and logarithmic floating-point iterations.

---

## 6. Dry Run

`stations = [1, 2, 3, 4, 5]`, $k = 4$. Initial gaps: `[1, 1, 1, 1]`, maxGap = 1.0

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | low = 0.0, high = 1.0 | ready |
| `Iter 1` | mid = 0.50. Each gap 1.0 needs floor(1.0/0.5)-1 = 1 station -> Total = 4 stations | count=4 <= 4 -> high = 0.50 |
| `Iter 2` | mid = 0.25. Each gap needs floor(1.0/0.25)-1 = 3 stations -> Total = 12 stations | count=12 > 4 -> low = 0.25 |
| `Iter 3..N` | Converges to exact threshold | Final Result = 0.500000 ✅ (add 1 station at 1.5, 2.5, 3.5, 4.5) |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- k is very large ($10^9$) -> Binary search handles instantly in $\approx 50$ iterations whereas Max-Heap fails.
- All initial gaps already equal.
- Precision exact boundary (`gap == count * dist`).

### Common Bugs to Avoid
- Forgetting `low = mid` and `high = mid` (in floating-point BS, do NOT use `mid + 1` or `mid - 1`).
- Floating-point rounding division error: forgetting the `gap == numberInBetween * dist` adjustment.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does floating point binary search NOT use low = mid + 1?**  
  **A**: Because the answer space is continuous (real numbers $\mathbb{R}$), not discrete integers. Adding $1$ skips an infinite number of real values. We simply set `high = mid` or `low = mid`.

- **Q2: How many iterations are needed to guarantee D decimal places?**  
  **A**: Each iteration halves the error interval. To achieve precision $\epsilon = 10^{-D}$, we need $\log_2(\frac{\text{initial\_range}}{10^{-D}})$ iterations (roughly $3.32 \times D$ iterations plus range exponent).

- **Q3: Why is Binary Search faster than Max-Heap when k is large?**  
  **A**: Heap takes $\mathcal{O}(k \log n)$, which depends linearly on $k$. Binary Search takes $\mathcal{O}(n \log(\text{range}/\epsilon))$, which is completely independent of $k$!

- **Q4: Can we run a fixed number of loop iterations (e.g. `for (int iter = 0; iter < 100; iter++)`)?**  
  **A**: Yes! Running a fixed loop of 80–100 iterations is a common competitive programming practice that eliminates floating-point epsilon precision edge cases.

- **Q5: What if we can place stations in a 2D highway network?**  
  **A**: In 2D planar networks, the problem transforms into the Voronoi Diagram / Continuous Facility Location Problem.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Search`, `BS on Answers`, `Floating Point`, `LeetCode-774`, `Hard`
- **Related problems to practice next**:
- **Aggressive Cows**: Discrete point separation counterpart.
- **Koko Eating Bananas**: Discrete rate search.
- **Median of Two Sorted Arrays**: Partition binary search.
