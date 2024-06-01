---
title: 【LeetCode】73. Set Matrix Zeroes 解題報告
categories:
  - LeetCode
date: 2021-08-24 23:35:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/1W445q3.jpg
---
 
# 73. Set Matrix Zeroes / Medium
Given an m x n integer matrix matrix, if an element is 0, set its entire row and column to 0's, and return the matrix.

You must do it in place.

<!-- more --> 
 

## Example 1:
![](https://assets.leetcode.com/uploads/2020/08/17/mat1.jpg)
> Input: matrix = [[1,1,1],[1,0,1],[1,1,1]]
> Output: [[1,0,1],[0,0,0],[1,0,1]]

## Example 2:
![](https://assets.leetcode.com/uploads/2020/08/17/mat2.jpg)
> Input: matrix = [[0,1,2,0],[3,4,5,2],[1,3,1,5]]
> Output: [[0,0,0,0],[0,4,5,0],[0,3,1,0]]


## Constraints: 
> - m == matrix.length
> - n == matrix[0].length
> - 1 <= m, n <= 200
> - -2^31 <= matrix[i][j] <= 2^31 - 1

# Solution
## 思路 

其實各種解法的思路應該都一樣，也就是把需要歸零的 row & column 都記錄下來，最後一次清零。
額外開一個 vector 會佔空間之外，最差的狀況會一直重複清零已經清零的區域。

第二種做法是，把 row 1 & column 1 當作是紀錄是否要清零的空間。這樣就可以減少使用的空間了。
空間複雜度可以降到 O(1)，不過實作上要注意 row 1 & column 1 本身清零的動作要最後才能做。

## 效能

### Complexity 
- Time Complexity: O(MN)
- Space Complexity: O(MN)

### LeetCode Result

- Runtime: 8 ms
- Memory Usage: 13.3 MB 
- https://leetcode.com/submissions/detail/473721919/

## Code
```cpp
class Solution {
public:
    void setZeroes(vector<vector<int>>& matrix) {
        int m = matrix.size();
        int n = matrix[0].size();
        
        vector<pair<int, int>> points;
        
        for(int i = 0; i < m; ++i) {
            for(int j = 0; j < n; ++j) {
                if(matrix[i][j] == 0)
                points.emplace_back(pair<int, int>(i, j));
            }
        }

        for(auto& point: points) {
            int x = point.first;
            int y = point.second;
            
            for(int i = 0; i < n; ++i) {
                matrix[x][i] = 0;
            }  
            for(int i = 0; i < m; ++i) {
                matrix[i][y] = 0;
            }   
        }
    }
};
```


```cpp
class Solution {
public:
    void setZeroes(vector<vector<int>>& matrix) {
        const int m = matrix.size();
        const int n = matrix[0].size();
        bool is_col_clear = false;
        // Find and mark the row/col that need to be zeroed.
        for(int i = 0; i < m; ++i) {
            if(matrix[i][0] == 0)
                is_col_clear = true;
            for(int j = 1; j < n; ++j) {
                if(matrix[i][j] == 0) {
                    matrix[i][0] = 0;
                    matrix[0][j] = 0;
                }
            }
        }
        
        for(int i = 1; i < m; ++i) {  
            for(int j = 1; j < n; ++j) {
                if(matrix[i][0] == 0 || matrix[0][j] == 0)
                    matrix[i][j] = 0;
            }
        }
            
        if(matrix[0][0] == 0) {
            for(int i = 0; i < n; ++i) {
                matrix[0][i] = 0;
            }
        }
        
        if(is_col_clear) {
            for(int i = 0; i < m; ++i) {
                matrix[i][0] = 0;
            }
        }
    }

};
```