---
title: 【LeetCode】54. Spiral Matrix 解題報告
categories:
  - LeetCode
date: 2021-09-05 23:47:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/LhYVrDR.jpg
---
 
# 54. Spiral Matrix / Medium
Given an m x n matrix, return all elements of the matrix in spiral order.

<!-- more --> 
 
## Example 1:
![](https://assets.leetcode.com/uploads/2020/11/13/spiral1.jpg)
> Input: matrix = [[1,2,3],[4,5,6],[7,8,9]]
> Output: [1,2,3,6,9,8,7,4,5]

## Example 2:
![](https://assets.leetcode.com/uploads/2020/11/13/spiral.jpg)
> Input: matrix = [[1,2,3,4],[5,6,7,8],[9,10,11,12]]
> Output: [1,2,3,4,8,12,11,10,9,5,6,7]

## Constraints: 
> - m == matrix.length
> - n == matrix[i].length
> - 1 <= m, n <= 10
> - -100 <= matrix[i][j] <= 100

# Solution: Set Boundary
## 思路 

每次旋轉的時候，就會往裡面前進。其實仔細觀察就會發現，每次移動到底，邊界都會內推。例如從 [0,0] -> [0,N] 之後，下次往右則是 [1,1] -> [1,N-1]。
所以我們可以設定四個邊界，每次按照 右 → 下 → 左 → 上 的順序移動，到邊界後停止，邊界內推。但解答的 vector 塞滿了 m * n 個後就可以離開迴圈，並且 resize 確保數量是吻合 m * n。

## 效能
### Complexity 
- Time Complexity: O(MN)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 0 ms
- Memory Usage: 6.7 MB 
- https://leetcode.com/submissions/detail/543875781/

## Code
```cpp
class Solution {
public:
    vector<int> spiralOrder(vector<vector<int>>& matrix) {
        vector<int> spiral;
        int m = matrix.size();
        int n = matrix[0].size();
        
        int left = 0;
        int top = 0;
        int right = n-1;
        int down = m-1;
        
        while(spiral.size() < m*n) {
            // Go right
            for(int i = left; i <= right; ++i) {
                spiral.emplace_back(matrix[top][i]);
            }
            ++top;
            
            // Go down
            for(int i = top; i <= down; ++i) {
                spiral.emplace_back(matrix[i][right]);
            }
            --right;
            
            // Go left
            for(int i = right; i >= left; --i) {
                spiral.emplace_back(matrix[down][i]);
            }
            --down;
            
            // Go up
            for(int i = down; i >= top; --i) {
                spiral.emplace_back(matrix[i][left]);
            }
            ++left;
        }
        spiral.resize(m*n);
        return spiral;
    }
};
```
