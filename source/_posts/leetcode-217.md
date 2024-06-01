---
title: 【LeetCode】217. Contains Duplicate 解題報告
categories:
  - LeetCode
date: 2021-08-21 14:56:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/LZfpE6z.jpg
---
 
# 217. Contains Duplicate / Medium

Given an integer array nums, return true if any value appears at least twice in the array, and return false if every element is distinct.


<!-- more --> 

## Example 1:
> Input: nums = [1,2,3,1]
> Output: true

## Example 2:
> Input: nums = [1,2,3,4]
> Output: false

## Example 3:
> Input: nums = [1,1,1,3,3,4,3,2,4,2]
> Output: true
 

## Constraints: 
> - 1 <= nums.length <= 10^5
> - 10^9 <= nums[i] <= 10^9


# Solution
## 思路

先排序，再檢查前後兩個元素是不是相等，相等就是有重複，可以直接回傳 true，迴圈結束的話就代表沒有重複，回傳 false。

## 效能

### Complexity 
- Time Complexity: O(NlogN)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 16 ms
- Memory Usage: 15.4 MB 
- https://leetcode.com/submissions/detail/541776499/

## Code
```cpp
class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        int n = nums.size();
        for(int i = 1; i < n; ++i) {
            if(nums[i] == nums[i-1]) return true;
        }
        return false;
    }
};
```

