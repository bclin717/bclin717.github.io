---
title: 【LeetCode】75. Sort Colors 解題報告
categories:
  - LeetCode
date: 2022-02-02 15:28:00
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/RYgB0rh.jpg
---
 
# 75. Sort Colors / Medium

Given an array nums with n objects colored red, white, or blue, sort them in-place so that objects of the same color are adjacent, with the colors in the order red, white, and blue.

We will use the integers 0, 1, and 2 to represent the color red, white, and blue, respectively.

You must solve this problem without using the library's sort function.

<!-- more --> 

## Example 1:
> Input: nums = [2,0,2,1,1,0]
> Output: [0,0,1,1,2,2]

## Example 2:
> Input: nums = [2,0,1]
> Output: [0,1,2]

## Comstraints
> - n == nums.length
> - 1 <= n <= 300
> - nums[i] is either 0, 1, or 2.

# Solution 1: Two Pointers
## 思路

左右兩個指標，分別代表 0 的右邊界以及 2 的左邊界。
一開始，左指標指向 0，右指標指向 nums.size()-1，第三個 cur 指標一開始指向 0。
如果 nums[cur] 是 0，就把 nums[cur] 與 nums[l] 互換，並將 cur 和 l 向右移動；
如果 nums[cur] 是 2，就把 nums[cur] 與 nums[r] 互換，並將 r 向左移動；
如果 nums[cur] 是 1，就把 cur 向右移動。
核心概念是，將 0 往左丟，2 往右丟，很像是有兩個 pivot 的 quick sort 的某次子排序。

## 效能

### Complexity 
- Time Complexity: O(N), which N is the size of nums.
- Space Complexity: O(1)

### LeetCode Result
- Runtime: 0 ms
- Memory Usage: 8.2 MB 
- https://leetcode.com/submissions/detail/632799396/

## Code
```cpp
class Solution {
public:
    void sortColors(vector<int>& nums) {
        int l = 0, r = nums.size()-1;
        int cur = 0;
        while(cur <= r) {
            if(nums[cur] == 0) {
                swap(nums[cur++], nums[l]);
                ++l;
            } else if(nums[cur] == 2) {
                swap(nums[cur], nums[r]);
                --r;
            } else {
                ++cur;
            }
        }
    }
};
```

# Solution 2: Simply Brute Force
## 思路

紀錄 0, 1, 2 分別有幾次，並且按照各自的順序與數量寫回 nums 陣列。

## 效能

### Complexity 
- Time Complexity: O(N), which N is the size of nums.
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 3 ms
- Memory Usage: 8.3 MB 
- https://leetcode.com/submissions/detail/632791666/

## Code
```cpp
class Solution {
public:
    void sortColors(vector<int>& nums) {
        int r = 0, w = 0, b = 0;
        for(const auto& color: nums) {
            if(color == 0) ++r;
            else if(color == 1) ++w;
            else if(color == 2) ++b;
        }
        
        int n = nums.size();
        for(int i = 0; i < n; ++i) {
            if(i < r) nums[i] = 0;
            else if(i >= r && i < w+r) nums[i] = 1;
            else nums[i] = 2;
        }
    }
};
```