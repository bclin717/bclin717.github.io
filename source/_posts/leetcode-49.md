---
title: 【LeetCode】49. Group Anagrams 解題報告
categories:
  - LeetCode
date: 2021-08-06 14:12:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/5F5TECO.jpg
---
 
# 49. Group Anagrams / Medium

Given an array of strings strs, group the anagrams together. You can return the answer in any order.

An Anagram is a word or phrase formed by rearranging the letters of a different word or phrase, typically using all the original letters exactly once.

<!-- more --> 
## Example 1:
> Input: strs = ["eat","tea","tan","ate","nat","bat"]
> Output: [["bat"],["nat","tan"],["ate","eat","tea"]]

## Example 2:
> Input: strs = [""]
> Output: [[""]]

## Example 3:
> Input: strs = ["a"]
> Output: [["a"]]

## Constraints:
> - 1 <= strs.length <= 104
> - 0 <= strs[i].length <= 100
> - strs[i] consists of lower-case English letters.


# Solution: Map
## 思路
開一個 map，key 是排序後的字串，value 是該字串在 vector 中對應的 index。
把每個字串排序後放進 map 查 index，再把沒排序過的 string 放到對應的 vector。

## 效能

### Complexity 
- Time Complexity: O(K*NlogN) where N is the length of string and K is number of strings.
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 20 ms
- Memory Usage: 18.2 MB 
- https://leetcode.com/submissions/detail/534088565/

## Code
```cpp
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        unordered_map<string, int> umap;
        vector<vector<string>> ans;
        int count = 0;
        for(const auto& str: strs) {
            string tmp(str);
            sort(tmp.begin(), tmp.end());
            if(umap.count(tmp) <= 0) {
                umap[tmp] = count;
                ++count;
                ans.emplace_back(vector<string>(0));
            } 
            ans[umap[tmp]].emplace_back(str);
        }
        return ans;
    }
};
```
