---
title: 【LeetCode】78. Subsets 解題報告
categories:
  - LeetCode
date: 2022-07-07 10:42:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/UsGmXCq.jpg
---
 
# 78. Subsets / Medium

Given an integer array nums of unique elements, return all possible subsets (the power set).

The solution set must not contain duplicate subsets. Return the solution in **any order**.

<!-- more --> 
 

## Example 1:
> Input: nums = [1,2,3]
> Output: [[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]

## Example 2:
> Input: nums = [0]
> Output: [[],[0]]

## Constraints: 
> - 1 <= nums.length <= 10
> - -10 <= nums[i] <= 10
> - All the numbers of nums are unique.

# Solution 1: Cascading
## 思路 

很直覺地，將之前的每一個組合拿出來跟目前的數字合成為新的組合

## 效能

### Complexity 
- Time Complexity: O(N*2^N)
- Space Complexity: O(N*2^N)

### LeetCode Result

- Runtime: 0 ms
- Memory Usage: 7.2 MB 
- https://leetcode.com/submissions/detail/731504399/

## Code
```cpp
class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        vector<vector<int>> result;
        
        for(int i = 0; i < nums.size(); ++i) {
            int size = result.size();
            for(int j = 0; j < size; j++) {
                vector<int> temp(result[j]);
                temp.emplace_back(nums[i]);
                result.emplace_back(temp);
            }
            result.emplace_back(vector<int>{nums[i]});
        }
        result.emplace_back(vector<int>{});
        return result;
    }
};
```

# Solution 2: Backtracking
## 思路 

利用 Backtracking 也可以很好的解掉這個問題。
每次遞迴時每一個都是一種組合，所以都要保留。

## 效能

### Complexity 
- Time Complexity: O(N*2^N)
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 3 ms
- Memory Usage: 7.5 MB 
- https://leetcode.com/submissions/detail/731537285/

## Code
```cpp
class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        gen(vector<int>(), nums, 0);
        return ans;
    }
    
    void gen(vector<int> now, vector<int>& nums, int level) {
        ans.emplace_back(now);
        if(nums.size() == level) return;
        for(int i = level; i < nums.size(); ++i) {
            now.emplace_back(nums[i]);
            gen(now, nums, i+1);
            now.pop_back();
        }
    }
private:
    vector<vector<int>> ans;
};
```