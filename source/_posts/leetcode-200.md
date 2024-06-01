---
title: 【LeetCode】200. Number of Islands 解題報告
categories:
  - LeetCode
date: 2022-07-05 22:51:00
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/MIxFhud.jpg
---

# 200. Number of Islands / Medium

Given an m x n 2D binary grid grid which represents a map of '1's (land) and '0's (water), return the number of islands.

An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.

<!-- more -->

## Example 1:
> Input: grid = [
> ["1","1","1","1","0"],
> ["1","1","0","1","0"],
> ["1","1","0","0","0"],
> ["0","0","0","0","0"]
> ]
> Output: 1

## Example 2:
> Input: grid = [
> ["1","1","0","0","0"],
> ["1","1","0","0","0"],
> ["0","0","1","0","0"],
> ["0","0","0","1","1"]
> ]
> Output: 3

## Constraints:
> - m == grid.length
> - n == grid[i].length
> - 1 <= m, n <= 300
> - grid[i][j] is '0' or '1'.

# Solution: DFS

## 思路

經典的 DFS 題目，每一格 1 都當作是 DFS 的起點，但如果遇到已經走過的就不再走。
DFS 的時候，往四個方向走，注意不要超出邊界。最後看總共從起點開始執行幾次 DFS。

## 效能
### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(N)

### LeetCode Result
- Runtime: 39 ms
- Memory Usage: 12.6 MB 
- https://leetcode.com/submissions/detail/739202125/

## Code 
```cpp
class Solution {
public:
    int numIslands(vector<vector<char>>& grid) {
        int sol = 0;
        m = grid.size();
        n = grid[0].size();
        visited = vector<vector<bool>>(m, vector<bool>(n, false));
        
        for(int i = 0; i < m; ++i) {
            for(int j = 0; j < n; ++j) {
                if(visited[i][j] == false && grid[i][j] == '1') {
                    dfs(grid, i, j);
                    ++sol;
                }
            }
        }
        return sol;
    }
    
    void dfs(vector<vector<char>>& grid, int x, int y) {
        if(x < 0 || x >= m || y < 0 || y >= n || visited[x][y] == true || grid[x][y] == '0') {
            return;
        }
        
        visited[x][y] = true;
        dfs(grid, x+1, y);
        dfs(grid, x-1, y);
        dfs(grid, x, y+1);
        dfs(grid, x, y-1);
    }
    
private:
    int m;
    int n;
    vector<vector<bool>> visited;
};
```
