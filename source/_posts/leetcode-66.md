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

基本上就是加法器的概念。
每位加起來等於除十的餘數。如果加起來超過十，Carry Flag（進位）就會設為 1。
最後要檢查 Carry Flag 還是不是設 1，是的話代表需要進位。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 0 ms
- Memory Usage: 8.7 MB 
- https://leetcode.com/submissions/detail/538757154/

## Code
```cpp
class Solution {
public:
    vector<int> plusOne(vector<int>& digits) {
        int n = digits.size();
        ++digits[n-1];
        bool carry = digits[n-1] == 10 ? true : false;
        if(carry == false) return digits;
        
        digits[n-1] = 0;
        for(int i = n-2; i >= 0; --i) {
            if(carry) {
                ++digits[i];
                carry = false;
            }
            
            if(digits[i] == 10) {
                digits[i] = 0;
                carry = true;
            }
        }
        
        if(carry) {
            digits.insert(digits.begin(), 1);
        }
        return digits;
    }
};
```