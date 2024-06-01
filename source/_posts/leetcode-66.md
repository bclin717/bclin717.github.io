---
title: 【LeetCode】66. Plus One 解題報告
categories:
  - LeetCode
date: 2021-08-22 22:03:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/pvRUSRz.jpg
---
 
# 66. Plus One / Easy

Given a non-empty array of decimal digits representing a non-negative integer, increment one to the integer.

The digits are stored such that the most significant digit is at the head of the list, and each element in the array contains a single digit.

You may assume the integer does not contain any leading zero, except the number 0 itself.
<!-- more --> 
 

## Example 1:
> Input: digits = [1,2,3]
> Output: [1,2,4]
> Explanation: The array represents the integer 123.

## Example 2:
> Input: digits = [4,3,2,1]
> Output: [4,3,2,2]
> Explanation: The array represents the integer 4321.

## Example 3:
> Input: digits = [0]
> Output: [1]

## Constraints: 
> - 1 <= digits.length <= 100
> - 0 <= digits[i] <= 9

# Solution
## 思路 

從最後一位開始看，如果小於 9，基本上加一後就可以回傳了。
如果大於 9，就設成零，以此類推。
最後如果走到迴圈結束都沒有回傳，就表示剛好加爆了，要進位，
在陣列最前面插入 1，回傳。


## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 0 ms
- Memory Usage: 8.8 MB 
- https://leetcode.com/submissions/detail/690241186/

## Code
```cpp
class Solution {
public:
    vector<int> plusOne(vector<int>& digits) {
        int n = digits.size()-1;
        
        for(int i = n; i >= 0; --i) {
            if(digits[i] < 9) {
                ++digits[i];
                return digits;
            }
            digits[i] = 0;
        }
        
        digits.insert(digits.begin(), 1);
        return digits;    
    }
};
```