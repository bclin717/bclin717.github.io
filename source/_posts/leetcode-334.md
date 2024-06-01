---
title: 【LeetCode】334. Increasing Triplet Subsequence 解題報告
categories:
  - LeetCode
date: 2022-06-19 21:12:56
tags: LeetCode
hidden: true
photos:
    -https://i.imgur.com/ovnVAbw.jpg
---
 
# 334. Increasing Triplet Subsequence / Medium

Given an integer array `nums`, return `true` if there exists a triple of indices `(i, j, k)` such that `i < j < k` and `nums[i] < nums[j] < nums[k]`. If no such indices exists, return `false`.

<!-- more --> 

## Example 1:
> Input: nums = [1,2,3,4,5]
> Output: true
> Explanation: Any triplet where i < j < k is valid.

## Example 2:
> Input: nums = [5,4,3,2,1]
> Output: false
> Explanation: No triplet exists.

# Example 3:
> Input: nums = [2,1,5,0,4,6]
> IOutput: true
> IExplanation: The triplet (3, 4, 5) is valid because nums[3] == 0 < nums[4] == 4 < nums[5] == 6.

## Comstraints
> -  `1 <= nums.length <= 5 * 10^5`
> -  `-2^31 <= nums[i] <= 2^31 - 1`

### Follow Up:
Follow up: Could you implement a solution that runs in O(n) time complexity and O(1) space complexity?

# Solution: Linear Scan
## 思路

這一題只要求我們給出是否存在這樣的三元遞增子序列，所以我們可以簡單的用兩個變數 `minNum1` 與 `minNum2`來測試。
如果我們找到一個數字小於 `minNum1`（最小的數字），那就替換掉；
如果有找到一個數字介於兩者之間，那就將 `minNum2` 換成這個數字。
接下來，一旦我們找到一個數字大於 `minNum2`，便意味著這樣的三元序列成形了，反之則是沒有存在這樣的序列。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 85 ms
- Memory Usage: 61.6 MB 
- https://leetcode.com/submissions/detail/706260381/

## Code
```cpp
class Solution {
public:
    bool increasingTriplet(vector<int>& nums) {
        int minNum = INT_MAX;
        int minNum2 = INT_MAX;
        for(const auto& num: nums) {
            if(num < minNum) {
                minNum = num;
            } 
            else if(num > minNum && num < minNum2) {
                minNum2 = num;
            }
            else if(num > minNum2) {
                return true;
            }
        }
        return false;
    }
};
```