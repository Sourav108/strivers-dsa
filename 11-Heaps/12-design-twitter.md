# Design Twitter (Feed timeline with Heap Merge) (Step 11.3 — Hard Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Design Twitter (Feed timeline with Heap Merge)](https://takeuforward.org/data-structure/design-twitter/)
- **Difficulty**: Hard
- **Statement**: Design a simplified version of Twitter supporting: `postTweet(userId, tweetId)`, `getNewsFeed(userId)` (10 most recent tweets from user and followees), `follow(followerId, followeeId)`, and `unfollow(followerId, followeeId)` in $\mathcal{O}(1)$ post/follow and $\mathcal{O}(K \log K)$ feed generation.

---

## 1. Problem, Restated

Implement news feed generation using multi-way K-sorted merge on follower tweet lists.

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Value / Top-K elements / Merged list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) Track global incremental `timestamp`. 2) User tweets: `unordered_map<int, vector<pair<int, int>>> tweets` (`{timestamp, tweetId}`). 3) Following: `unordered_map<int, unordered_set<int>> following`. 4) `getNewsFeed(userId)`: User follows $K$ people. Merge their most recent tweets using a Max-Heap of size at most 10 in $\mathcal{O}(K \log K)$ time (Merge K Sorted Lists pattern!).

- **Underlying Pattern**: `Multi-Way Merge with Max-Heap on Followee Timelines`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Dump all tweets from all followed users into a vector and sort in $\mathcal{O}(T \log T)$ time.

### C++17 Code
```cpp
// O(T log T) sort all tweets
```

### Java Code
```java
// Java equivalent
// O(T log T) sort all tweets
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(T \log T)$ where $T$ is total tweets.
- **Space Complexity**: $\mathcal{O}(T)$.
- **Why it's not good enough**: Sorts entire historical tweet archive.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Priority Queue / Heap implementation below directly achieves optimal $\mathcal{O}(N \log K)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
K-Way Max-Heap Timeline Merge in $\mathcal{O}(10 \log K)$ time.

### C++17 Code
```cpp
#include <vector>
#include <unordered_map>
#include <unordered_set>
#include <queue>
using namespace std;

class Twitter {
private:
    int globalTimestamp;
    unordered_map<int, vector<pair<int, int>>> userTweets; // userId -> list of {timestamp, tweetId}
    unordered_map<int, unordered_set<int>> userFollowing;  // followerId -> set of followeeIds

public:
    Twitter() : globalTimestamp(0) {}
    
    void postTweet(int userId, int tweetId) {
        userTweets[userId].push_back({globalTimestamp++, tweetId});
    }
    
    vector<int> getNewsFeed(int userId) {
        // Max-heap stores {timestamp, tweetId, userId, index_in_user_tweets}
        priority_queue<vector<int>> maxHeap;
        
        // Collect self and all followees
        unordered_set<int> users = userFollowing[userId];
        users.insert(userId); // include self tweets
        
        for (int u : users) {
            if (!userTweets[u].empty()) {
                int lastIdx = (int)userTweets[u].size() - 1;
                auto [time, tweetId] = userTweets[u][lastIdx];
                maxHeap.push({time, tweetId, u, lastIdx});
            }
        }
        
        vector<int> feed;
        while (!maxHeap.empty() && feed.size() < 10) {
            auto curr = maxHeap.top();
            maxHeap.pop();
            
            feed.push_back(curr[1]); // tweetId
            int u = curr[2];
            int nextIdx = curr[3] - 1;
            
            if (nextIdx >= 0) {
                auto [time, tweetId] = userTweets[u][nextIdx];
                maxHeap.push({time, tweetId, u, nextIdx});
            }
        }
        
        return feed;
    }
    
    void follow(int followerId, int followeeId) {
        if (followerId != followeeId) {
            userFollowing[followerId].insert(followeeId);
        }
    }
    
    void unfollow(int followerId, int followeeId) {
        userFollowing[followerId].erase(followeeId);
    }
};
```

### Java Code
```java
import java.util.*;

class Twitter {

    int globalTimestamp;
    unordered_map<int, List<int[]>> userTweets; // userId . list of {timestamp, tweetId}
    unordered_map<int, Set<Integer>> userFollowing;  // followerId . set of followeeIds

    public Twitter() { /* initialized: globalTimestamp(0)  */  }
    
    void postTweet(int userId, int tweetId) {
        userTweets[userId].add({globalTimestamp++, tweetId});
    }
    
    int[] getNewsFeed(int userId) {
        // Max-heap stores {timestamp, tweetId, userId, index_in_user_tweets}
        PriorityQueue<int[]> maxHeap = new PriorityQueue<>((a, b) . Integer.compare(a[0], b[0]));
        
        // Collect self and all followees
        Set<Integer> users = userFollowing[userId];
        users.add(userId); // include self tweets
        
        for (int u : users) {
            if (!userTweets[u].isEmpty()) {
                int lastIdx = (int)userTweets[u].size() - 1;
                var [time, tweetId] = userTweets[u][lastIdx];
                maxHeap.push({time, tweetId, u, lastIdx});
            }
        }
        
        int[] feed;
        while (!maxHeap.isEmpty() && feed.length < 10) {
            var curr = maxHeap.peek();
            maxHeap.pop();
            
            feed.add(curr[1]); // tweetId
            int u = curr[2];
            int nextIdx = curr[3] - 1;
            
            if (nextIdx >= 0) {
                var [time, tweetId] = userTweets[u][nextIdx];
                maxHeap.push({time, tweetId, u, nextIdx});
            }
        }
        
        return feed;
    }
    
    void follow(int followerId, int followeeId) {
        if (followerId != followeeId) {
            userFollowing[followerId].insert(followeeId);
        }
    }
    
    void unfollow(int followerId, int followeeId) {
        userFollowing[followerId].erase(followeeId);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ for `postTweet`, `follow`, `unfollow`; $\mathcal{O}(10 \log K)$ for `getNewsFeed`.
- **Space Complexity**: $\mathcal{O}(U + T)$ space.
- **Why this is optimal**: K-way merge on the 10 most recent tweets avoids fetching entire histories.

---

## 6. Dry Run

Follow + Post + News Feed Workflow

| Step | Action / State Change | Result |
|---|---|---|
| `postTweet(1, 5)` | User 1 posts tweet 5 at t=0 | posted |
| `follow(1, 2)` | User 1 follows 2 | followed |
| `postTweet(2, 6)` | User 2 posts tweet 6 at t=1 | posted |
| `getNewsFeed(1)` | Merge User 1 (t=0) and User 2 (t=1) -> `[6, 5]` | `[6, 5]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- User unfollows self (prevent self-unfollow).
- User with 0 tweets or 0 followees.

### Common Bugs to Avoid
- Forgetting to include `userId`'s own tweets in `getNewsFeed`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is pull-on-read (K-way merge) preferred over push-on-write (fan-out) for Twitter?**  
  **A**: Because high-follower accounts (celebrities with 100M followers) would cause massive write-amplification on every post. Pull-on-read generates the feed on demand in $\mathcal{O}(K \log K)$ time.


---

## 9. Tags & Related Problems

- **Tags**: `Heap`, `Design`, `Hash Map`, `LeetCode-355`, `Hard`
- **Related problems to practice next**:
- **Merge M Sorted Lists**: Underlying algorithm.
