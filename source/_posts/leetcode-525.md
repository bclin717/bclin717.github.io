---
title: 【LeetCode】525. Contiguous Array 解題報告
categories:
  - LeetCode
date: 2022-08-01 14:54:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/ZkPbBaT.jpg
---
 
# 525. Contiguous Array / Medium
Given a binary array nums, return the maximum length of a contiguous subarray with an equal number of 0 and 1.
 
<!-- more --> 

## Example 1:
> Input: nums = [0,1]
> Output: 2
> Explanation: [0, 1] is the longest contiguous subarray with an equal number of 0 and 1.

## Example 2:
> Input: nums = [0,1,0]
> Output: 2
> Explanation: [0, 1] (or [1, 0]) is a longest contiguous subarray with equal number of 0 and 1.
> 
## Constraints: 
> - 1 <= nums.length <= 10^5
> - nums[i] is either 0 or 1.

# Solution
## 思路 

我們把 0 當成 -1，並且不斷計算目前的和。
例如 [0, 1] 那就是先 -1 再變成 0，也就是說如果整個加起來為 0，但 0, 1 的數量一定相等。
又或者是，如果之前曾經加起來變成 -2，現在這裡加起來也是 -2，代表中間這段其實是零。
我們利用 HashMap 紀錄每一個的和的最新位置。如果我們遇到他們之前出現過，就計算中間這段的長度。
最長的即為答案。

值得注意的是，第 hmap[0] 必須是 -1，因為如果現在加起來為零，計算長度時要算入整段。
`i - hmap[count]` 才會是答案。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 199 ms
- Memory Usage: 83.9 MB 
- https://leetcode.com/submissions/detail/762110793/

## Code
```cpp
class Solution {
public:
    int findMaxLength(vector<int>& nums) {
        int res = 0;
        unordered_map<int, int> hmap;
        hmap[0] = -1;
        int count = 0;
        for(int i = 0; i < nums.size(); ++i) {
            count = count + (nums[i] == 1 ? 1 : -1);
            if(hmap.count(count) == 0) hmap[count] = i;
            else {
                res = max(res, i - hmap[count]);
            }
        }
        return res;
    }
};
```