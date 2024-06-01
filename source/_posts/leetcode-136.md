---
title: 【LeetCode】136. Single Number 解題報告
categories:
  - LeetCode
date: 2021-08-22 00:23:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/qOz0LMl.jpg
---
 
# 136. Single Number / Easy

Given a non-empty array of integers nums, every element appears twice except for one. Find that single one.

You must implement a solution with a linear runtime complexity and use only constant extra space.
<!-- more --> 


## Example 1:
> Input: nums = [2,2,1]
> Output: 1

## Example 2:
> Input: nums = [4,1,2,1,2]
> Output: 4

## Example 3:
> Input: nums = [1]
> Output: 1

## Constraints: 
> - 1 <= nums.length <= 3 * 10^4
> - -3 * 10^4 <= nums[i] <= 3 * 10^4
> - Each element in the array appears twice except for one element which appears only once.

# Solution
## 思路

這題有很多有趣的解法，但題目要求空間複雜度必須是O(1)，時間複雜度是O(N)，這就限制了這題的解法。要判斷是否出現過，可能需要 set、map 或至少 array，不使用額外的空間真的很困難。

有一個有趣的解法是利用 xor 的特性，同樣的數 xor 後會變成 0。題目已經提到，只有一個元素出現一次，其他都有兩次，如此一來，我們遍歷整個陣列，將所有的數字 xor 起來，最後留下來的就是答案了。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 26 ms
- Memory Usage: 16.8 MB 
- https://leetcode.com/submissions/detail/538411396/

## Code
```cpp
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int n = nums[0];
        for(int i = 1; i < nums.size(); ++i) {
            n ^= nums[i];
        }
        return n;
    }
};
```