---
title: 【LeetCode】62. Unique Paths 解題報告
categories:
  - LeetCode
date: 2021-08-09 11:06:56
tags: LeetCode
photos:
    - https://i.imgur.com/TlXmCxi.jpg
---
 
# 62. Unique Paths / Medium

A robot is located at the top-left corner of a m x n grid (marked 'Start' in the diagram below).

The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid (marked 'Finish' in the diagram below).

How many possible unique paths are there?
 

<!-- more --> 
## Example 1:
![](https://assets.leetcode.com/uploads/2018/10/22/robot_maze.png)
> Input: m = 3, n = 7
> Output: 28

## Example 2:
> Input: m = 3, n = 2
> Output: 3
> Explanation:
> From the top-left corner, there are a total of 3 ways to reach the bottom-right corner:
> 1. Right -> Down -> Down
> 2. Down -> Down -> Right
> 3. Down -> Right -> Down

## Example 3:
> Input: m = 7, n = 3
> Output: 28

## Example 4:
> Input: m = 3, n = 3
> Output: 6

## Constraints:
> - 1 <= m, n <= 100
> - It's guaranteed that the answer will be less than or equal to 2 * 109.
 


# Solution: DP
## 思路

一開始想用 DFS，但會超時，改用 DP 紀錄路徑的數量。
能抵達格子 [m, n] 的路徑數量為 dp[m-1][n] + dp[m][n-1]，也就是上面加左邊，因為機器人只能向右或向下走。

## 效能

### Complexity 
- Time Complexity: O(MN)
- Space Complexity: O(MN)

### LeetCode Result

- Runtime: 0 ms
- Memory Usage: 5.8 MB 
- https://leetcode.com/submissions/detail/535537874/

## Code
```cpp
class Solution {
public:
    int uniquePaths(int m, int n) {
        int dp[m][n];
        for(int i = 0; i < m; ++i) {
            for(int j = 0; j < n; ++j) {
                if(i == 0) dp[i][j] = 1;
                else if(j == 0) dp[i][j] = 1;
                else dp[i][j] = 0;
            }
        }
        
        for(int i = 1; i < m; ++i) {
            for(int j = 1; j < n; ++j) {
                dp[i][j] = dp[i-1][j] + dp[i][j-1];
            }
        }
        
        return dp[m-1][n-1];
    
    }
};
```