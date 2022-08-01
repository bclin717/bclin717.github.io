---
title: 【LeetCode】221. Maximal Square 解題報告
categories:
  - LeetCode
date: 2022-08-01 12:35:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/Ra7xZSF.jpg
---
 
# 221. Maximal Square / Medium

Given an m x n binary matrix filled with 0's and 1's, find the largest square containing only 1's and return its area.

<!-- more --> 
 

## Example 1:
![](https://assets.leetcode.com/uploads/2020/11/26/max1grid.jpg)
> Input: matrix = [["1","0","1","0","0"],["1","0","1","1","1"],["1","1","1","1","1"],["1","0","0","1","0"]]
> Output: 4

## Example 2:
![](https://assets.leetcode.com/uploads/2020/11/26/max2grid.jpg)
> Input: matrix = [["0","1"],["1","0"]]
> Output: 1

## Example 3:
> Input: matrix = [["0"]]
> Output: 0

## Constraints: 
> - m == matrix.length
> - n == matrix[i].length
> - 1 <= m, n <= 300
> - matrix[i][j] is '0' or '1'.

# Solution 1: DP with 2D vector
## 思路 

我們用 DP 來儲存某一格能組出最長的方格的長度。
如果要 dp[i][j] 組成方格的話，dp[i-1][j], dp[i-1][j-1], dp[i][j-1] 都必須大於 1，才有機會組成方格，
因此 dp[i][j] 的值即是 dp[i-1][j], dp[i-1][j-1], dp[i][j-1] 中的最小值。
另外我們必須判斷邊緣，也就是 row = 0 以及 col = 0 時，直接將 matrix 中的數字當成是 dp 的數字，才不會讓 index 超出邊界。
要注意的是，某一個 cell 是 1 的話，他自己就是一個 1x1 的方格。

## 效能

### Complexity 
- Time Complexity: O(MN)
- Space Complexity: O(MN)

### LeetCode Result
- Runtime: 96 ms
- Memory Usage: 18.2 MB 
- https://leetcode.com/submissions/detail/761313211/

## Code
```cpp
class Solution {
public:
    int maximalSquare(vector<vector<char>>& matrix) {
        int m = matrix.size();
        int n = matrix[0].size();
        vector<vector<int>> dp(m+1, vector<int>(n+1, 0));
        int maxsqlen = 0;
        
        for(int i = 0; i < m; ++i) {
            for(int j = 0; j < n; ++j) {
                if(i == 0 || j == 0 || matrix[i][j] == '0') {
                    dp[i][j] = matrix[i][j] - '0';
                } else {
                    dp[i][j] = min(dp[i-1][j], min(dp[i][j-1],dp[i-1][j-1])) + 1;
                }
                maxsqlen = max(maxsqlen, dp[i][j]);
            }
        }
        return maxsqlen*maxsqlen;
    }
};
```

# Solution 2: DP with 1D vector
## 思路 

由於每一次我們只用到兩條 row 的資訊，可以不用儲存整個二維陣列。
而上一個 row 的資訊會被帶下來，實際上需要紀錄的只有左上角那一格的資訊，
所以我們可以用一維陣列搭配一個變數來進行 DP。

## 效能

### Complexity 
- Time Complexity: O(MN)
- Space Complexity: O(N)

### LeetCode Result
- Runtime: 74 ms
- Memory Usage: 16.4 MB 
- https://leetcode.com/submissions/detail/762025491/

## Code
```cpp
class Solution {
public:
    int maximalSquare(vector<vector<char>>& matrix) {
        int m = matrix.size();
        int n = matrix[0].size();
        int pre;
        vector<int> dp(n, 0);
        
        int maxlen = 0;
        for(int i = 0; i < m; ++i) {
            for(int j = 0; j < n; ++j) {
                int tmp = dp[j];
                if(i == 0 || j == 0 || matrix[i][j] == '0') {
                    dp[j] = matrix[i][j] - '0';
                } else {
                    dp[j] = min(dp[j-1], min(dp[j], pre)) + 1;
                }
                maxlen = max(maxlen, dp[j]);
                pre = tmp;
            }
        }
        return maxlen * maxlen;
    }
};
```