---
title: 【LeetCode】26. Remove Duplicates from Sorted Array 解題報告
categories:
  - LeetCode
date: 2021-08-14 11:23:56
tags: LeetCode
photos:
    - 
---
 
# 26. Remove Duplicates from Sorted Array / Easy

<!-- more --> 

## Example 1:

## Example 2:

## Example 3:


# Solution: 
## 思路

## 效能

### Complexity 
- Time Complexity: O(N) 
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 7 ms
- Memory Usage: 18.4 MB 
- https://leetcode.com/submissions/detail/538368359/

## Code
```cpp
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        int n = nums.size();
        if(0 == n) return 0;

        int cnt = 1;
        for(int i = 0; i < n-1; ++i) {
            if(nums[i] != nums[i+1]) {
                nums[cnt] = nums[i+1];
                ++cnt;
            }
        }
        return cnt;
    }
};
```