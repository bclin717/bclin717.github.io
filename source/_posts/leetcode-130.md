---
title: 【LeetCode】130. Surrounded Regions 解題報告
categories:
  - LeetCode
date: 2022-06-14 16:45:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/VkBgsPN.jpg
---
 
# 130. Surrounded Regions / Medium

Given an `m x n` matrix `board` containing `'X'` and `'O'`, capture all regions that are 4-directionally surrounded by `'X'`.

A region is **captured** by flipping all `'O'`s into `'X'`s in that surrounded region.

<!-- more --> 


## Example 1:
![](https://assets.leetcode.com/uploads/2021/02/19/xogrid.jpg)
> Input: board = [["X","X","X","X"],["X","O","O","X"],["X","X","O","X"],["X","O","X","X"]]
> Output: [["X","X","X","X"],["X","X","X","X"],["X","X","X","X"],["X","O","X","X"]]
> Explanation: Surrounded regions should not be on the border, which means that any 'O' on the border of the board are not flipped to 'X'. Any 'O' that is not on the border and it is not connected to an 'O' on the border will be flipped to 'X'. Two cells are connected if they are adjacent cells connected horizontally or vertically.

## Example 2:
> Input: board = [["X"]]
> Output: [["X"]]

## Constraints: 
> - m == board.length
> - n == board[i].length
> - 1 <= m, n <= 200
> - board[i][j] is 'X' or 'O'.


# Solution: DFS
## 思路

這題其實跟 200.Number of Islands 很像，同樣使用 DFS 來解決就行了。
不同的是，必須要上下左右都是 X 才能變成 X。於是只要有和邊緣連線的，通通都不變。
根據這項原則，我們先把與邊緣相連的部分換成 E，之後再將所有的 O 換成 X。
此舉的原因是，不與邊緣相連的 O 一定會變成 X

最後再與邊緣相連，絕對不會變成 X 的 E 換成 O。非常簡單。

## 效能

### Complexity 
- Time Complexity: O(N), where N is the number of cells in the board. In the worst case where it contains only the O cells on the board.
- Space Complexity: O(N), where N is the number of cells in the board. 

### LeetCode Result
- Runtime: 15 ms
- Memory Usage: 9.9 MB 
- https://leetcode.com/submissions/detail/721223772/

## Code
```cpp
class Solution {
public:
    void solve(vector<vector<char>>& board) {
        m = board.size();
        n = board[0].size();
        
        vector<pair<int, int>> edgePoints;
        for(int i = 0; i < m; ++i) {
            for(int j = 0; j < n; ++j) {
                if(i == 0 || j == 0 || i == m-1 || j == n-1) {
                    edgePoints.emplace_back(pair<int, int>{i, j});
                }
            }
        }
        
        for(const auto& edgePoint: edgePoints) {
            int x = edgePoint.first;
            int y = edgePoint.second;
            if(board[x][y] == 'O') {
                dfs(board, x, y);
            }
        }
        
        for(int i = 0; i < m; ++i) {
            for(int j = 0; j < n; ++j) {
                if(board[i][j] == 'E') {
                    board[i][j] = 'O';
                } else {
                    board[i][j] = 'X';
                }
            }
        }
    }
    
    void dfs(vector<vector<char>>& board, int x, int y) {
        if(x < 0 || x >= m || y < 0 || y >= n || board[x][y] == 'X' || board[x][y] == 'E') {
            return;
        }
        
        board[x][y] = 'E';
        dfs(board, x+1, y);
        dfs(board, x-1, y);
        dfs(board, x, y+1);
        dfs(board, x, y-1);
    }
    
private:
    int m;
    int n;
    
};
```