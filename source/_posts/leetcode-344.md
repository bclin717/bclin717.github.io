---
title: 【LeetCode】344. Reverse String 解題報告
categories:
  - LeetCode
date: 2022-01-10 7:52:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/j2l4NEB.jpg
---
 
# 344. Reverse String / Easy

Write a function that reverses a string. The input string is given as an array of characters s.

You must do this by modifying the input array in-place with O(1) extra memory.

<!-- more --> 

## Example 1:
> Input: s = ["h","e","l","l","o"]
> Output: ["o","l","l","e","h"]

## Example 2:
> Input: s = ["H","a","n","n","a","h"]
> Output: ["h","a","n","n","a","H"]


## Comstraints
> - 1 <= s.length <= 10^5
> - s[i] is a printable ascii character.

# Solution
## 思路

設定兩個指標，一個從頭，一個從尾，不斷向中央靠近，同時交換元素，交會後即完成反轉。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 16 ms
- Memory Usage: 23.3 MB 
- https://leetcode.com/submissions/detail/616176658/

## Code
```cpp
class Solution {
public:
    void reverseString(vector<char>& s) {
        int r = s.size()-1;
        int l = 0;
        while(l < r) swap(s[l++], s[r--]);
    }
};
```