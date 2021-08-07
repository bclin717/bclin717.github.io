---
title: 【LeetCode】53. Maximum Subarray 解題報告
categories:
  - LeetCode
date: 2021-08-07 10:06:56
tags: LeetCode
photos:
    - https://i.imgur.com/5F5TECO.jpg
---
 
# 53. Maximum Subarray / Medium

Given an integer array nums, find the contiguous subarray (containing at least one number) which has the largest sum and return its sum.

A subarray is a contiguous part of an array.

<!-- more --> 
## Example 1:
> Input: nums = [-2,1,-3,4,-1,2,1,-5,4]
> Output: 6
> Explanation: [4,-1,2,1] has the largest sum = 6.

## Example 2:
> Input: nums = [1]
> Output: 1
> 
## Example 3:
> Input: nums = [5,4,-1,7,8]
> Output: 23

## Constraints:
> -  1 <= nums.length <= 3 * 104
> - -105 <= nums[i] <= 105
 

# Solution: Map
## 思路
題目不需要找出子陣列，只要找出最大的和，所以直接一個一個加起來最快。
如果目前的總和小於目前的數字，代表前面加起來是負的，直接把目前的數字設成總和就可以了。


## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 4 ms
- Memory Usage: 13 MB 
- https://leetcode.com/problems/maximum-subarray/

## Code
```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int sum = 0;
        int maxNum = INT_MIN;
        for(int num: nums) {
            sum += num;
            if(num > sum) sum = num;
            maxNum = max(maxNum, sum);
        }
        return maxNum;
    }
};
```
