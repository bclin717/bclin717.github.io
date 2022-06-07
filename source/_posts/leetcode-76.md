---
title: 【LeetCode】76. Minimum Window Substring 解題報告
categories:
  - LeetCode
date: 2022-06-07 21:12:00
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/yYOIS94.jpg
---
 
# 76. Minimum Window Substring / Hard

Given two strings s and t of lengths m and n respectively, return the minimum window substring of s such that every character in t (including duplicates) is included in the window. If there is no such substring, return the empty string "".

The testcases will be generated such that the answer is unique.

A substring is a contiguous sequence of characters within the string.

<!-- more --> 

## Example 1:
> Input: s = "ADOBECODEBANC", t = "ABC"
> Output: "BANC"
> Explanation: The minimum window substring "BANC" includes 'A', 'B', and 'C' from string t.

## Example 2:
> Input: s = "a", t = "a"
> Output: "a"
> Explanation: The entire string s is the minimum window.

## Example 3:
> Input: s = "a", t = "aa"
> Output: ""
> Explanation: Both 'a's from t must be included in the window.
> Since the largest window of s only has one 'a', return empty string.

## Constraints:
> - m == s.length
> - n == t.length
> - 1 <= m, n <= 105
> - s and t consist of uppercase and lowercase English letters.

# Solution: Map + Sliding Window
## 思路

先利用 Hashmap 將字串 t 的字元出現次數都存下來，
接下來不斷移動右邊的指標，用 cnt 來記錄扣對（s 的字元也出現在 t 而且沒有扣超過的狀況）的情況，
也就是 
> --hmap[s[r]] >= 0
如果 cnt 和 t 的長度相等，代表目前的範圍已經涵蓋了 s 所有的字元。
這時，讓左指標往右走，計算長度是否是目前最短的，同時將剛剛扣掉的字元從 hashmap 加回來。

## 效能

### Complexity 
- Time Complexity: O(M+N), which M and N are the length of string S and T
- Space Complexity: O(M+N)

### LeetCode Result
- Runtime: 4 ms
- Memory Usage: 7.6 MB 
- https://leetcode.com/submissions/detail/711371925/

## Code
```cpp
class Solution {
public:
    string minWindow(string s, string t) {
        int hmap[128] = {0};
        int l = 0, r = 0, cnt = 0;
        int min_len = INT_MAX, min_start = -1;

        for(const auto& c: t) ++hmap[c];
        for(int r = 0; r < s.size(); ++r) {
            if(--hmap[s[r]] >= 0) ++cnt;
            while(cnt == t.size()) {
                if(r-l+1 < min_len) {
                    min_start = l;
                    min_len = r-l+1;
                }
                if(++hmap[s[l]] > 0) --cnt;
                ++l;
            }
        }
        return min_start == -1 ? "" : s.substr(min_start, min_len);
    }
};

```