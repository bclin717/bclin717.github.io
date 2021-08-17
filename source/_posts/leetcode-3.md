---
title: 【LeetCode】3. Longest Substring Without Repeating Characters 解題報告
categories:
  - LeetCode
date: 2021-07-07 21:55:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/0yI4u3T.jpg
---

# 3. Longest Substring Without Repeating Characters / Medium

Given a string s, find the length of the longest substring without repeating characters.

<!-- more -->

## Example 1:
> Input: s = "abcabcbb"
> Output: 3
> Explanation: The answer is "abc", with the length of 3.

## Example 2:
> Input: s = "bbbbb"
> Output: 1
> Explanation: The answer is "b", with the length of 1.

## Example 3:
> Input: s = "pwwkew"
> Output: 3
> Explanation: The answer is "wke", with the length of 3.
> Notice that the answer must be a substring, "pwke" is a subsequence and not a substring.

## Example 4:
> Input: s = ""
> Output: 0

# Solution 1: Sliding Window / Brute Force
## 思路

最長不重複子字串，字串內包含所有字元。因此我們知道，不重複子字串最長 128 字元。

i, j 兩個指標代表目前的子字串頭尾，j 往前掃，每次都將新進的字元位置存進 chars 陣列中，如果之前已經有過這個字元，就會有兩種狀況：
1. 這個字元目前還在子字串中（chars[s[j]] >= i）
2. 字元已經離開子字串（chars[s[j]] < i）
我們取最大的那一個變成目前子字串的開頭，即 i 不動或往右移動，接著計算子字串長度，大於目前的 maxLen 就更新 maxLen 的值，然後將 s[j] 字元的位置更新成 j+1。


## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(N)

### LeetCode Result
- Runtime: 4 ms
- Memory Usage: 6.9 MB 
- https://leetcode.com/submissions/detail/518287814/

## Code 
```cpp
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        if(s.empty()) return 0;
        int n = s.size();
        int chars[129] = {0};
        int maxLen = 1;
        for(int i = 0, j = 0; j < n; ++j) {
            if(j - i >= 128) return 128;
            if(chars[s[j]] != 0) i = max(i, chars[s[j]]);
            maxLen = max(maxLen, j-i+1);
            chars[s[j]] = j+1;
        }
        return maxLen;
    }
};
```