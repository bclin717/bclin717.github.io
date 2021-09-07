---
title: 【LeetCode】36. Valid Sudoku 解題報告
categories:
  - LeetCode
date: 2021-08-23 22:03:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/3YJNymS.jpg
---
 
# 36. Valid Sudoku / Easy

Determine if a 9 x 9 Sudoku board is valid. Only the filled cells need to be validated according to the following rules:
1. Each row must contain the digits 1-9 without repetition.
2. Each column must contain the digits 1-9 without repetition.
3. Each of the nine 3 x 3 sub-boxes of the grid must contain the digits 1-9 without repetition.

## Note:
- A Sudoku board (partially filled) could be valid but is not necessarily solvable.
- Only the filled cells need to be validated according to the mentioned rules.


<!-- more --> 
 

## Example 1:
![](https://upload.wikimedia.org/wikipedia/commons/thumb/f/ff/Sudoku-by-L2G-20050714.svg/250px-Sudoku-by-L2G-20050714.svg.png)
> Input: board = 
> [["5","3",".",".","7",".",".",".","."]
> ,["6",".",".","1","9","5",".",".","."]
> ,[".","9","8",".",".",".",".","6","."]
> ,["8",".",".",".","6",".",".",".","3"]
> ,["4",".",".","8",".","3",".",".","1"]
> ,["7",".",".",".","2",".",".",".","6"]
> ,[".","6",".",".",".",".","2","8","."]
> ,[".",".",".","4","1","9",".",".","5"]
> ,[".",".",".",".","8",".",".","7","9"]]
> Output: true

## Example 2:
> Input: board = 
> [["8","3",".",".","7",".",".",".","."]
> ,["6",".",".","1","9","5",".",".","."]
> ,[".","9","8",".",".",".",".","6","."]
> ,["8",".",".",".","6",".",".",".","3"]
> ,["4",".",".","8",".","3",".",".","1"]
> ,["7",".",".",".","2",".",".",".","6"]
> ,[".","6",".",".",".",".","2","8","."]
> ,[".",".",".","4","1","9",".",".","5"]
> ,[".",".",".",".","8",".",".","7","9"]]
> Output: false
> Explanation: Same as Example 1, except with the 5 in the top left corner being modified to 8. Since there are two 8's in the top left 3x3 sub-box, it is invalid.

## Constraints: 
> - board.length == 9
> - board[i].length == 9
> - board[i][j] is a digit or '.'.

# Solution
## 思路 

要檢查數獨是否 valid，需要檢查每一行 row 、 column 還有九宮格。
最簡單的寫法就是每一行都暴力判斷，或是用 set 把每一行的數字存起來，判斷是否重複。
但我們可以用比較省空間的方式來存，數獨的長寬固定是 9，我們可以用 9 個 bits 來儲存數字是否出現的狀態。這樣每一行列僅需要 2^9 = 512 大小的空間即可。

## 效能

### Complexity 
- Time Complexity: O(N^2)
- Space Complexity: O(N^2)

### LeetCode Result

- Runtime: 16 ms
- Memory Usage: 17.9 MB 
- https://leetcode.com/submissions/detail/538845337/

## Code
```cpp
class Solution {
public:
    bool isValidSudoku(vector<vector<char>>& board) {
        const int N = 9;
        bitset<N> rows[N];
        bitset<N> cols[N];
        bitset<N> boxes[N];
        
        for(int i = 0; i < N; ++i) {
            for(int j = 0; j < N; ++j) {
                char val = board[i][j];
                if(val == '.') continue;
                bitset<N> pos = 0;
                pos[val-'1'] = 1;
                
                if((rows[i] & pos).any()) {
                    return false;
                }
                rows[i] |= pos;
                
                if((cols[j] & pos).any()) {
                    return false;
                }
                cols[j] |= pos;
                
                int box_idx = ((i/3)*3) + (j/3);
                if((boxes[box_idx] & pos).any()) {
                    return false;
                }
                boxes[box_idx] |= pos;
            }
        }        
        return true;
    }
};
```