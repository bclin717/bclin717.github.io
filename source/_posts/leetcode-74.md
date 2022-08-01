---
title: 【LeetCode】74. Search a 2D Matrix 解題報告
categories:
  - LeetCode
date: 2021-08-01 12:35:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/Mt6dsH1.jpg
---
 
# 74. Search a 2D Matrix / Medium
Write an efficient algorithm that searches for a value target in an m x n integer matrix matrix. This matrix has the following properties:

- Integers in each row are sorted from left to right.
- The first integer of each row is greater than the last integer of the previous row.

<!-- more --> 
 

## Example 1:
![](https://assets.leetcode.com/uploads/2020/10/05/mat.jpg)
> Input: matrix = [[1,3,5,7],[10,11,16,20],[23,30,34,60]], target = 3
> Output: true

## Example 2:
![](https://assets.leetcode.com/uploads/2020/10/05/mat2.jpg)
> Input: matrix = [[1,3,5,7],[10,11,16,20],[23,30,34,60]], target = 13
> Output: false

## Constraints: 
> - m == matrix.length
> - n == matrix[i].length
> - 1 <= m, n <= 100
> - -10^4 <= matrix[i][j], target <= 10^4

# Solution: Binary Search
## 思路 

這題不難，其實就是把整個二維陣列當成一維陣列，再用 Binary Search 做搜尋。
至於如何計算出 Index，row 的部分是直接除，col 的部分則是餘數。

## 效能

### Complexity 
- Time Complexity: O(log(mn))
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 3 ms
- Memory Usage: 9.5 MB 
- https://leetcode.com/submissions/detail/761572851/

## Code
```cpp
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int m = matrix.size();
        int n = matrix[0].size();
        int left = 0, right = (m*n)-1;
        int pivotIdx, pivotElement;
        while(left <= right) {
            pivotIdx = left + (right - left) / 2;
            pivotElement = matrix[pivotIdx / n][pivotIdx % n];
            if(target == pivotElement) return true;
            if(target < pivotElement) right = pivotIdx - 1;
            else left = pivotIdx + 1;
        }
        return false;
    }
};
```