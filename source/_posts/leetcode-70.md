---
title: 【LeetCode】70. Climbing Stairs 解題報告
categories:
  - LeetCode
date: 2022-06-28 20:18:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/1AeLkKr.jpg
---
 
# 70. Climbing Stairs / Easy

You are climbing a staircase. It takes n steps to reach the top.

Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?
<!-- more --> 
 

## Example 1:
> Input: n = 2
> Output: 2
> Explanation: There are two ways to climb to the top.
> 1. 1 step + 1 step
> 2. 2 steps

## Example 2:
> Input: n = 3
> Output: 3
> Explanation: There are three ways to climb to the top.
> 1. 1 step + 1 step + 1 step
> 2. 1 step + 2 steps
> 3. 2 steps + 1 step


## Constraints: 
> - 1 <= n <= 45

# Solution
## 思路 

爬梯子這題是很經典的 DP 題目，適合用來理解 DP 的概念。
每次只能爬 1 階或 2 階，意思就是如果要爬到第 3 階，只要能夠爬到第 1 階或第 2 階就可以了。
至於幾種爬法，只要將前兩階的爬法加起來就是現在這階的爬法數量。
也就是說，狀態轉移方程式為
`dp[i] = dp[i-1] + dp[i-2]`
而初始狀態則是第 1 與第 2 階，都是一種爬法。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 0 ms
- Memory Usage: 6.3 MB 
- https://leetcode.com/submissions/detail/732186624/

## Code
```cpp
class Solution {
public:
    int climbStairs(int n) {
        vector<int> dp(n+1, 0);
        dp[0] = 1; dp[1] = 1;
        for(int i = 2; i <= n; ++i) {
            dp[i] = dp[i-1] + dp[i-2];
        }
        return dp[n];
    }
};
```