---
title: 【LeetCode】340. Longest Substring with At Most K Distinct Characters 解題報告
categories:
  - LeetCode
date: 2022-05-31 07:36:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/6ruEpMu.jpg
---
 
# 340. Longest Substring with At Most K Distinct Characters / Medium

Given a string s and an integer k, return the length of the longest substring of s that contains at most k distinct characters.

<!-- more --> 


## Example 1:
> Input: s = "eceba", k = 2
> Output: 3
> Explanation: The substring is "ece" with length 3.

## Example 2:
> Input: s = "aa", k = 1
> Output: 2
> Explanation: The substring is "aa" with length 2.

## Constraints: 
> - 1 <= s.length <= 5 * 10^4
> - 0 <= k <= 50

# Solution 1: Sliding Window + Hashmap
## 思路

用 Sliding Window 是一個很直覺的做法，搭配 hashmap 來紀錄出現過的字元及其數量。
最差的狀況下，出現 N 個不同的字元，從 hashmap 中移除時都需要 O(k) 的時間複雜度，則會有 O(Nk) 的時間複雜度。
若要優化的話，可以使用類似 LRU Cache 的資料結構來替代 hashmap。


## 效能

### Complexity 
- Time Complexity: O(Nk), which N is the length of s. In the worst case it takes O(k) to erase an element in the unordered_map.
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 7 ms
- Memory Usage: 7.1 MB 
- https://leetcode.com/submissions/detail/710348643/

## Code
```cpp
class Solution {
public:
    int lengthOfLongestSubstringKDistinct(string s, int k) {
        if(k == 0) return 0;
        if(s.size() < k) return s.size();
        const int n = s.size();
        unordered_map<char, int> hmap;
        int max_len = 0;
        for(int l = 0, r = 0; r < n; ++r) {
            ++hmap[s[r]];
            if(hmap.size() <= k) {
                max_len = max(max_len, r-l+1);
            } else {
                --hmap[s[l]];
                if(hmap[s[l]] == 0) hmap.erase(s[l]);
                ++l;
            }
        }
        return max_len;
    }
};
```
