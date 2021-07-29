---
title: 【LeetCode】15. 3Sum 解題報告
categories:
  - LeetCode
date: 2021-07-29 21:24:56
tags: LeetCode
photos:
    - https://i.imgur.com/MrU60D4.jpg
---
 
# 15. 3Sum / Medium

Given an integer array nums, return all the triplets [nums[i], nums[j], nums[k]] such that i != j, i != k, and j != k, and nums[i] + nums[j] + nums[k] == 0.

Notice that the solution set must not contain duplicate triplets.

<!-- more --> 
## Example 1:
> Input: nums = [-1,0,1,2,-1,-4]
> Output: [[-1,-1,2],[-1,0,1]]

## Example 2:
> Input: nums = []
> Output: []

## Example 3:
> Input: nums = [0]
> Output: []
 

## Constraints:
> 0 <= nums.length <= 3000
> -105 <= nums[i] <= 105

# Solution: Three Pointers
## 思路

這題基本上是 Two sum 的變體，其實只要固定一個數就變成 Two Sum 了。
因為求的不是 index 而是數字集合，所以我們可以先排序。

首先嘗試暴力解，需要三個迴圈，會超時，所以我們需要減少搜索空間。

一個迴圈固定一個數，迴圈兩個指標左右包夾剩下的區域，因為有排序過，所以如果目前指標計算出來的值小於 0，代表負數比較大，左邊的指標要右移，反之則是正數比較大，右邊的指標要左移。

找到解的時候，因為結果必須是不重複的，我們可以直接迴圈略過鄰近相同的數。


## 效能

### Complexity 
- Time Complexity: O(N^2)
- Space Complexity: O(N^2)

### LeetCode Result

- Runtime: 60 ms
- Memory Usage: 19.9 MB 
- https://leetcode.com/submissions/detail/530118816/

## Code 
```cpp
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        int n = nums.size();
        if(n < 3) return vector<vector<int>>(0);
        vector<vector<int>> ans(0);
        sort(nums.begin(), nums.end());
        
        if(nums[0] > 0) return ans;
        for(int i = 0; i < n-2; ++i) {
            int left = i+1, right = n-1;
            while(i < n-2 && nums[i] == nums[i+1]) ++i;
            while(left < right) {
                int sum = nums[i] + nums[left] + nums[right];
                if(sum == 0) {
                    ans.emplace_back(vector<int>{nums[i], nums[left], nums[right]});
                    while(left < right && nums[left] == nums[left+1]) ++left;
                    while(left < right && nums[right] == nums[right-1]) --right;
                    if(left == right) break;
                    ++left;
                    --right;
                } else if (sum < 0) { 
                    ++left;
                } else {
                    --right;
                }
            }
        }
        return ans;
    }
};
```
