---
title: 【LeetCode】283. Move Zeroes 解題報告
categories:
  - LeetCode
date: 2021-08-22 22:09:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/vTU0V9F.jpg
---
 
# 283. Move Zeroes / Easy

Given an integer array nums, move all 0's to the end of it while maintaining the relative order of the non-zero elements.

Note that you must do this in-place without making a copy of the array.
<!-- more --> 
 

## Example 1:
> Input: nums = [0,1,0,3,12]
> Output: [1,3,12,0,0]

## Example 2:
> Input: nums = [0]
> Output: [0]

## Constraints: 
> - 1 <= nums.length <= 10^4
> - -2^31 <= nums[i] <= 2^31 - 1
> 
# Solution
## 思路 

跟 [26. Remove Duplicates from Sorted Array](https://bclin.tw/2021/08/20/leetcode-26/) 有點像，用兩個指標，一前一後移動，一遇到 0，就把兩個指標的內容互換。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 0 ms
- Memory Usage: 9 MB 
- https://leetcode.com/submissions/detail/538798757/

## Code
```cpp
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        for(int i = 0, j = 0; j < nums.size(); ++j) {
            if(nums[i] != 0) {
                ++i;
            } else if(nums[i] == 0 && nums[j] != 0) {
                swap(nums[i], nums[j]);
                ++i;
            }
        }
    }
};
```