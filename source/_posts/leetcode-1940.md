---
title: 【LeetCode】1940. Longest Common Subsequence Between Sorted Arrays 解題報告
categories:
  - LeetCode
date: 2024-06-01 15:00:00
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/rnOYfnu.jpeg
---
 
# 1940. Longest Common Subsequence Between Sorted Arrays / Medium

Given an array of integer arrays arrays where each arrays[i] is sorted in strictly increasing order, return an integer array representing the longest common subsequence between all the arrays.

A subsequence is a sequence that can be derived from another sequence by deleting some elements (possibly none) without changing the order of the remaining elements.

<!-- more --> 

## Example 1:
> Input: arrays = [[1,3,4],[1,4,7,9]]
> Output: [1,4]
> Explanation: 
> The longest common subsequence in the two arrays is [1,4].

## Example 2:
> Input: arrays = [[2,3,6,8],[1,2,3,5,6,7,10],[2,3,4,6,9]]
> Output: [2,3,6]
> Explanation: The longest common subsequence in all three arrays is [2,3,6].

## Example 3:
> Input: arrays = [[1,2,3,4,5],[6,7,8]]
> Output: []
> Explanation: There is no common subsequence between the two arrays.

## Comstraints
> - 2 <= arrays.length <= 100
> - 1 <= arrays[i].length <= 100
> - 1 <= arrays[i][j] <= 100
> - arrays[i] is sorted in strictly increasing order.

# Solution: Two Pointer
## 思路

如果寫過 LCS 相關的題目，可能會想用 DP 來解。比較簡單一點的做法，可以用 Two pointer 來比較任意兩個 array，得到 LCS。之後繼續比較剩下的 array，直到比較完為止。這麼做的好處是很簡單，而且時間複雜度可以控制在 O(MN)。


## 效能

### Complexity 
- Time Complexity: O(NM), N is the size of "arrays" and M is the longest array length.
- Space Complexity: O(N), N is the size of temp. 

### LeetCode Result

- Runtime: 27 ms
- Memory Usage: 21.64 MB 
- https://leetcode.com/problems/longest-common-subsequence-between-sorted-arrays/submissions/1273933773/

## Code
```cpp
class Solution {
public:
    vector<int> longestCommonSubsequence(vector<vector<int>>& arrays) {
        vector<int> res = arrays[0];
        int n = arrays.size();
        for(int i = 1; i < n; ++i) 
            res = getLCS(res, arrays[i]);
        return res;
    }

    vector<int> getLCS(vector<int> vec1, vector<int> vec2) {
        int n = vec1.size(), m = vec2.size();
        int p1 = 0, p2 = 0;
        vector<int> temp;
        while(p1 < n && p2 < m) {
            if(vec1[p1] == vec2[p2]) {
                temp.emplace_back(vec1[p1]);
                ++p1, ++p2;
            } else if(vec1[p1] > vec2[p2]) 
                ++p2;
            else 
                ++p1;
        }
        return temp;
    }
};
```