---
title: 【LeetCode】287. Find the Duplicate Number 解題報告
categories:
  - LeetCode
date: 2022-05-31 17:37:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/H3IWurL.jpg
---
 
# 287. Find the Duplicate Number / Medium

Given an array of integers nums containing n + 1 integers where each integer is in the range [1, n] inclusive.

There is only one repeated number in nums, return this repeated number.

You must solve the problem without modifying the array nums and uses only constant extra space.

<!-- more --> 
 

## Example 1:
> Input: nums = [1,3,4,2,2]
> Output: 2

## Example 2:
> Input: nums = [3,1,3,4,2]
> Output: 3

## Constraints:
> - 1 <= n <= 10^5
> - nums.length == n + 1
> - 1 <= nums[i] <= n
> - All the integers in nums appear only once except for precisely one integer which appears two or more times.

# Solution: Floyd's Tortoise and Hare
## 思路 

這題有趣的地方在於，題目要求不能修改 array，而且也不能使用額外的空間。
原來有的幾種解法：
- Sort: 修改了 Array
- Set, Map: 使用了額外空間
- Negative Tagging: 修改了 Array

可以使用的解法：
- Binary Search: O(NlogN)
- Sum Bits: O(NlogN)
- Floyd's Tortoise and Hare (Cycle Detection): O(N)

我們在此只介紹 Floyd's Tortoise and Hare，這是一個在 Graph 裡常用的 Cycle Detection 演算法，他不只能快速找出是否有 Cycle，還可以找出 Cycle 的點位。

將題目所給的陣列，當作是一張圖，如下圖：

![](https://leetcode.com/problems/find-the-duplicate-number/Figures/287/complex_cycle.png)

其演算法設計了兩個指標，兔子（Hare, 快指標）走的速度是烏龜（Tortoise, 慢指標），如果兔子和烏龜會相遇，代表有迴圈存在。
以這題來說，它們必定會相遇，所以不需要考慮沒有相遇的情況。

接著，兔子留在原處，烏龜從原點再走一次，這次兩個指標的走速一樣，當兩者相遇，即是迴圈的入口處。

證明的方式也很簡單

![](https://leetcode.com/problems/find-the-duplicate-number/Figures/287/first_intersection.png)

如圖，設從起點到迴圈入口為 F，迴圈長度 C，烏龜走了 a。
第一階段，兔子的速度是烏龜的兩倍，於是 烏龜 2(F+a) = 兔子 F+a + nC。（最後的nC代表烏龜可能已經領先了n圈）。
他們的相遇點就會是 F+a = nC
第二階段，兩者等速行走，烏龜從起點到迴圈入口要走 F，於是 F+a + F = nC + F，得證，兩者的相會處，即兔子與烏龜再走 F 步，就會是迴圈的入口。

理解後，實作就很簡單了，直接看 code 就會懂。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 107 ms
- Memory Usage: 61.3 MB 
- https://leetcode.com/submissions/detail/710282465/

## Code
```cpp
class Solution {
public:
    int findDuplicate(vector<int>& nums) {
        int fast = nums[0];
        int slow = nums[0];
        do {
            fast = nums[nums[fast]];
            slow = nums[slow];
        } while(fast != slow);
        
        slow = nums[0];
        while(fast != slow) {
            fast = nums[fast];
            slow = nums[slow];
        }
        return fast;
    }
};
```