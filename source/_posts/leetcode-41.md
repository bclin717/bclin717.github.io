---
title: 【LeetCode】41. First Missing Positive 解題報告
categories:
  - LeetCode
date: 2022-05-30 07:07:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/krOAxyQ.jpg
---
 
# 41. First Missing Positive / Hard

Given an unsorted integer array nums, return the smallest missing positive integer.

You must implement an algorithm that runs in O(n) time and uses constant extra space.

<!-- more --> 
## Example 1:
> Input: nums = [1,2,0]
> Output: 3

## Example 2:
> Input: nums = [3,4,-1,1]
> Output: 2

## Example 3:
> Input: nums = [7,8,9,11,12]
> Output: 1

## Constraints
> - 1 <= nums.length <= 5 * 10^5
> - -2^31 <= nums[i] <= 2^31 - 1


# Solution 1: In-place HashMap / Negative tagging
## 思路

由於題目要求要在 O(N) 的時間複雜度以及常數空間複雜度解決，我們就無法另外宣告一個 Hashmap 了。
這種情況，通常都要直接用原來的 vector 做標記。由於題目是要判別正整數，所以我們可以用標記為負數來代表該數是否存在。
例如 index 1 的值是負的，代表 1 存在，index 2 的值是正的，代表 2 不存在，以此類推。

要實作這個思路，有兩個問題要考慮

1. 原來的負數
原來的負數與大於 N (陣列size) 的值都應該被處理掉，因為按題意來說，如果陣列大小為 N，那遺失的正整數最大也不過就是 N+1。
所以我們先將原來的負數以及大於 N 的數都改為 1。
然而這樣會迎來另一個問題，如果題目本來沒有 1，結果改完後反而多出 1 怎麼辦？
這個特殊的 case，我們可以直接解掉，先遍歷陣列一次，如果本來沒有 1，那就可以回傳 1。

2. Index-i 存 i，那誰存 N？
用 Index 0 存 N 就好，所以最後判別誰大於 0 時，迴圈必須從 1 開始，最後才檢查 index 0。
如果都是負的，那就回傳 N+1。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 283 ms
- Memory Usage: 83 MB 
- https://leetcode.com/submissions/detail/709743928/

## Code
```cpp
class Solution {
public:
    int firstMissingPositive(vector<int>& nums) {
        const int n = nums.size();
        
        bool contains_one = false;
        for(const auto& num: nums) {
            if(num == 1) {
                contains_one = true;
                break;
            }
        }
        
        if(!contains_one) return 1;
        
        // Data Cleanup
        for(auto& num: nums) {
            if(num <= 0 || num > n) {
                num = 1;
            }
        }
        
        // Negative Tagging (index 0 stores number n)
        for(int i = 0; i < n; ++i) {
            int a = abs(nums[i]);
            if(a == n) nums[0] = - abs(nums[0]);
            else nums[a] = - abs(nums[a]);
        }
        
        for(int i = 1; i < n; ++i) {
            if(nums[i] > 0) return i;
        }
        
        if(nums[0] > 0) return n;
        return n+1;
    }
};
```

# Solution 2: Sort with Swapping
## 思路

第二個思路是，我們可以先排序，讓 index i 存 i+1
我們可以用 Swap 來達成這個目標。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 178 ms
- Memory Usage: 82.9 MB 
- https://leetcode.com/submissions/detail/709745092/

## Code
```cpp
class Solution {
public:
    int firstMissingPositive(vector<int>& nums) {
        const int n = nums.size();
        for(int i = 0; i < n; ++i) {
            while(nums[i] > 0 && nums[i] <= n && nums[nums[i] - 1] != nums[i]) {
                swap(nums[nums[i] - 1], nums[i]);
            }
        }
        
        for(int i = 0; i < n; ++i) {
            if(nums[i] != i+1) return i+1;
        }
        
        return n+1;
    }
};
```