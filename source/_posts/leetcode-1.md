---
title: 【LeetCode】1. Two Sum 解題報告
categories:
  - LeetCode
date: 2021-07-12 16:50:00
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/MdmnTmq.jpg
---

# 1. Two Sum / Easy

Given an array of integers nums and an integer target, return indices of the two numbers such that they add up to target.

You may assume that each input would have exactly one solution, and you may not use the same element twice.

You can return the answer in any order.

<!-- more -->

## Example 1:
> Input: nums = [2,7,11,15], target = 9
> Output: [0,1]
> Output: Because nums[0] + nums[1] == 9, we return [0, 1].

## Example 2:
> Input: nums = [3,2,4], target = 6
> Output: [1,2]

## Example 3:
> Input: nums = [3,3], target = 6
> Output: [0,1]

## Constraints:
> - 2 <= nums.length <= 10^4
> - -10^9 <= nums[i] <= 10^9
> - -10^9 <= target <= 10^9
> - Only one valid answer exists.

# Solution 1: Brute Force

## 思路

暴力解應該很直覺，雙迴圈，第一個數字用 target 剪去目前的數字，再迴圈去找第二個數字。優點是簡單，缺點是很慢。

## 效能

### Complexity 
- Time Complexity: O(N^2)
- Space Complexity: O(1)

### LeetCode Result
- Runtime: 276 ms
- Memory Usage: 10.2 MB 
- https://leetcode.com/submissions/detail/521189952/

## Code 
```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        int n = nums.size();
        for(int i = 0; i < n; ++i) {
            for(int j = i+1; j < n; ++j) {
                if(nums[i] + nums[j] == target) {
                    return vector<int>{i, j};
                }
            }
        }
        return vector<int>{0};
    }
};
```

# Solution 2: Hash Map

## 思路

暴力解當中，要找到另一個補數需要迴圈找，太耗時間，那就用 Hash Map 把這些數字的位置存起來。Hash Map 尋找的時間是 O(1)，於是我們就可以把整個搜尋時間變成 O(N)了。

迴圈開始跑，數字進來，先找找 Map 中有沒有補數，有的話，我們就找到答案了；沒有的話，把目前的數字存進 Map，以供之後尋找。

由於題目宣稱一定會有一組解，所以可以放心地在找到解之後，馬上把解送出去。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(N)

### LeetCode Result
- Runtime: 9 ms
- Memory Usage: 10.9 MB 
- https://leetcode.com/submissions/detail/690811629/

## Code 
```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> hmap;
        for(int i = 0; i < nums.size(); ++i) {
            if(hmap.count(target-nums[i]) != 0) return {i, hmap[target-nums[i]]};
            hmap[nums[i]] = i;
        }
        return {0,0};
    }
};
```