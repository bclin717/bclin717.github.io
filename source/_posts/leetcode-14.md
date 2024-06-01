---
title: 【LeetCode】14. Longest Common Prefix 解題報告
categories:
  - LeetCode
date: 2021-09-07 15:39:00
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/lX9zTKQ.jpg
---

# 14. Longest Common Prefix / Easy

Write a function to find the longest common prefix string amongst an array of strings.

If there is no common prefix, return an empty string "".

<!-- more -->

## Example 1:
> Input: strs = ["flower","flow","flight"]
> Output: "fl"

## Example 2:
> Input: strs = ["dog","racecar","car"]
> Output: ""
> Explanation: There is no common prefix among the input strings.

## Constraints:
> - 1 <= strs.length <= 200
> - 0 <= strs[i].length <= 200
> - strs[i] consists of only lower-case English letters.

# Solution: Vertical Scanning

## 思路

把第一個字串當作是 prefix，開始一個一個字串比對是不是 prefix。
利用 find，如果在目前的字串中沒有找到，或是找到的位置不是 0（代表不是 prefix），就把 prefix 最後面一個字元砍掉，再比對一次，直到找到或是字串被砍光了。如果有找到，就接下去和後面的字串比對。

## 效能

### Complexity 
- Time Complexity: O(MN), where M is the size of strs and N is the size of each string.
- Space Complexity: O(1)

### LeetCode Result
- Runtime: 4 ms
- Memory Usage: 9.2 MB 
- https://leetcode.com/submissions/detail/539743387/

## Code 
```cpp
class Solution {
public:
    string longestCommonPrefix(vector<string>& strs) {
        string prefix = strs[0];
        for(int i = 1; i < strs.size(); ++i) {
            int pos = strs[i].find(prefix);
            while(string::npos == pos || 0 != pos) {
                prefix = prefix.substr(0, prefix.size()-1);
                pos = strs[i].find(prefix);
            }
            if(strs[i] == "") return "";
        }
        return prefix;
    }
};
```