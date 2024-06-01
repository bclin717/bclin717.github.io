---
title: 【LeetCode】28. Implement strStr() 解題報告
categories:
  - LeetCode
date: 2021-09-07 15:39:00
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/VnxkwBg.jpg
---

# 28. Implement strStr() / Easy

Implement strStr().

Return the index of the first occurrence of needle in haystack, or -1 if needle is not part of haystack.

### Clarification:

What should we return when needle is an empty string? This is a great question to ask during an interview.

For the purpose of this problem, we will return 0 when needle is an empty string. This is consistent to C's strstr() and Java's indexOf().

 
<!-- more -->

## Example 1:
> Input: haystack = "hello", needle = "ll"
> Output: 2

## Example 2:
> Input: haystack = "aaaaa", needle = "bba"
> Output: -1

## Eample 3:
> Input: haystack = "", needle = ""
> Output: 0

## Constraints:
> - 0 <= haystack.length, needle.length <= 5 * 10^4
> - haystack and needle consist of only lower-case English characters.

# Solution: Vertical Scanning

## 思路

利用迴圈掃一輪 haystack，僅比較 needle 的第一個字是不是目前的字元，如果是，就把 haystack 從 i 切到 i + needle.size()，比較看看是不是 needle。掃到底都不是就回傳 -1。

## 效能

### Complexity 
- Time Complexity: O(MN), where M is the length of haystack and N is the length of needle (rarely happened)
- Space Complexity: O(1)

### LeetCode Result
- Runtime: 68 ms
- Memory Usage: 368.7 MB 
- https://leetcode.com/submissions/detail/539424352/

## Code 
```cpp
class Solution {
public:
    int strStr(string haystack, string needle) {
        if(needle.empty()) return 0;
        
        const int size_of_needle = needle.size();
        const int size_of_haystack = haystack.size();
        const int size_diff = size_of_haystack - size_of_needle;
        
        for(int i = 0; i <= size_diff ; ++i) {
            if(haystack[i] == needle[0]) {
                if(haystack.substr(i, size_of_needle) == needle) {
                    return i;
                }
            }
        }
        return -1;
    }
};
```