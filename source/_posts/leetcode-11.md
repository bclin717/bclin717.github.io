---
title: 【LeetCode】11. Container With Most Water 解題報告
categories:
  - LeetCode
date: 2021-07-27 10:25:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/LRcgcGr.jpg
---
 
# 11. Container With Most Water / Medium

Given n non-negative integers a1, a2, ..., an , where each represents a point at coordinate (i, ai). n vertical lines are drawn such that the two endpoints of the line i is at (i, ai) and (i, 0). Find two lines, which, together with the x-axis forms a container, such that the container contains the most water.

Notice that you may not slant the container.

<!-- more --> 
## Example 1:
![](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/07/17/question_11.jpg)
> Input: height = [1,8,6,2,5,4,8,3,7]
> Output: 49
> Explanation: The above vertical lines are represented by array [1,8,6,2,5,4,8,3,7]. In this case, the max area of water (blue section) the container can contain is 49.

## Example 2:
> Input: height = [1,1] 
> Output: 1

## Example 3:
> Input: height = [4,3,2,1,4] 
> Output: 16

## Example 4:
> Input: height = [1,2,1] 
> Output: 2
 

## Constraints:
> n == height.length 
> 2 <= n <= 105 
> 0 <= height[i] <= 104

# Solution: Two Pointer Approach
## 思路
因為雙迴圈暴力列舉一定會超時，所以得用類似 Greedy 的解法，左右兩個指標，計算目前圍起來的面積，計算完之後，紀錄是否比目前最大面積還大。接著，比較短的那一邊指標向中心縮，直到兩個指標交會。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 68 ms
- Memory Usage: 58.9 MB 
- https://leetcode.com/submissions/detail/528627317/

## Code 
```cpp
class Solution {
public:
    int maxArea(vector<int>& height) {
        int n = height.size();
        if(n == 2) return min(height[0], height[1]);
        int maxArea = 0;
        int left = 0, right = n-1;
        while(left < right) {
            maxArea = max(min(height[left], height[right]) * (right-left), maxArea);
            if(height[left] < height[right]) {
                ++left;
            } else {
                --right;
            }
        }

        return maxArea;
    }
};
```
