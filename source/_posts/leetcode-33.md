---
title: 【LeetCode】33. Search in Rotated Sorted Array 解題報告
categories:
  - LeetCode
date: 2021-08-03 17:30:56
tags: LeetCode
hidden: true
hidden: true
photos:
    - https://i.imgur.com/c0LQYpw.jpg
---
 
# 33. Search in Rotated Sorted Array / Medium

There is an integer array nums sorted in ascending order (with distinct values).

Prior to being passed to your function, nums is rotated at an unknown pivot index k (0 <= k < nums.length) such that the resulting array is [nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]] (0-indexed). For example, [0,1,2,4,5,6,7] might be rotated at pivot index 3 and become [4,5,6,7,0,1,2].

Given the array nums after the rotation and an integer target, return the index of target if it is in nums, or -1 if it is not in nums.

You must write an algorithm with O(log n) runtime complexity.

<!-- more --> 
## Example 1:
> Input: nums = [4,5,6,7,0,1,2], target = 0
> Output: 4

## Example 2:
> Input: nums = [4,5,6,7,0,1,2], target = 3
> Output: -1

## Example 3:
> Input: nums = [1], target = 0
> Output: -1

## Constraints
> - 1 <= nums.length <= 5000
> - -10^4 <= nums[i] <= 10^4
> - All values of nums are unique.
> - nums is guaranteed to be rotated at some pivot.
> - -10^4 <= target <= 10^4

# Solution 1: Brute Force (Optimized)
## 思路
題目寫要 O(log n) 的複雜度，理論上應該是要找出中間，判斷 target 是在前段還是後段，然後用 binary search，但這題寫出來不知為什麼沒比較快，又比較複雜，果斷放棄 XD

暴力解還是最直覺的，從頭到尾掃雖然也可以過，但可以透過判斷結尾的元素是大於還是小於 target，來得知 target 可能的位置在前段還是後段。
如果是後段就倒著掃回來，最差的狀況就是 pivot 很接近左右兩邊，結果就是幾乎等於要掃一輪。 

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 4 ms
- Memory Usage: 11 MB 
- https://leetcode.com/submissions/detail/532572046/

## Code
```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int n = nums.size();
        if(nums[n-1] >= target) {
            for(int i = n-1; i >= 0; --i) {
                if(target == nums[i]) return i;
                if(nums[i] < target) return -1;
            }
            return -1;
        } else {
            for(int i = 0; i < n; ++i) {
                if(target == nums[i]) return i;
                if(nums[i] > target) return -1;
            }
            return -1;
        }
    }
};
```

# Solution 2: STL find
## 思路
使用 STL 的 find，理論上也是 linear search，但速度卻比較快 XD
N 夠大的話應該會跟暴力解一樣快才對，但寫起來比較漂亮。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 0 ms
- Memory Usage: 11 MB 
- https://leetcode.com/submissions/detail/532571892/

## Code
```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        auto it = find(nums.begin(), nums.end(), target);
        if(it == nums.end()) return -1;
        return distance (nums.begin(), it);
    }
};
```

