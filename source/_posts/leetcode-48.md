---
title: 【LeetCode】48. Rotate Image 解題報告
categories:
  - LeetCode
date: 2021-08-24 14:38:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/HWuCQr1.jpg
---
 
# 48. Rotate Image / Medium
You are given an n x n 2D matrix representing an image, rotate the image by 90 degrees (clockwise).

You have to rotate the image in-place, which means you have to modify the input 2D matrix directly. DO NOT allocate another 2D matrix and do the rotation.


<!-- more --> 
 

## Example 1:
![](https://assets.leetcode.com/uploads/2020/08/28/mat1.jpg)
> Input: matrix = [[1,2,3],[4,5,6],[7,8,9]]
> Output: [[7,4,1],[8,5,2],[9,6,3]]

## Example 2:
![](https://assets.leetcode.com/uploads/2020/08/28/mat2.jpg)
> Input: matrix = [[5,1,9,11],[2,4,8,10],[13,3,6,7],[15,14,12,16]]
> Output: [[15,13,2,5],[14,3,4,1],[12,6,8,9],[16,7,10,11]]

## Example 3:
> Input: matrix = [[1]]
> Output: [[1]]

## Example 4:
> Input: matrix = [[1,2],[3,4]]
> Output: [[3,1],[4,2]]


## Constraints: 
> - matrix.length == n
> - matrix[i].length == n
> - 1 <= n <= 20
> - -1000 <= matrix[i][j] <= 1000

# Solution 1: Brute Force
## 思路 

計算每個格子旋轉後的位置，但 index 的部分容易出錯。很可能留 bug 在裡面

## 效能

### Complexity 
- Time Complexity: O(N^2)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 0 ms
- Memory Usage: 7.1 MB 
- https://leetcode.com/submissions/detail/538885885/

## Code
```cpp
class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {
        const int n = matrix[0].size();
        for(int i = 0; i < n/2 + n%2; ++i) {
            for(int j = 0; j < n/2; ++j) {
                int tmp = matrix[n-1-j][i];
                matrix[n-1-j][i] = matrix[n-1-i][n-j-1];
                matrix[n-1-i][n-j-1] = matrix[j][n-1-i];
                matrix[j][n-1-i] = matrix[i][j];
                matrix[i][j] = tmp;
            }
        }
            
    }
};
```

# Solution 2: Transpose & Reverse
## 思路 

比較容易的方法是：先取轉置（transpose）後的矩陣，再 reverse 每個 row。就可以達到旋轉九十度的效果了。

## 效能

### Complexity 
- Time Complexity: O(N^2)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 0 ms
- Memory Usage: 7.1 MB 
- https://leetcode.com/submissions/detail/538890662/

## Code
```cpp
class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {
        const int n = matrix[0].size();
        for(int i = 0; i < n; ++i) {
            for(int j = i+1; j < n; ++j) {
                swap(matrix[i][j], matrix[j][i]);
            }
            reverse(matrix[i].begin(), matrix[i].end());
        }
    }
};
```