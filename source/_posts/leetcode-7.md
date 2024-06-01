---
title: 【LeetCode】7. Reverse Integer 解題報告
categories:
  - LeetCode
date: 2021-09-06 21:52:00
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/5NIVIuJ.jpg
---

# 7. Reverse Integer / Easy

Given a signed 32-bit integer x, return x with its digits reversed. If reversing x causes the value to go outside the signed 32-bit integer range [-231, 231 - 1], then return 0.

#### Assume the environment does not allow you to store 64-bit integers (signed or unsigned).

<!-- more -->

## Example 1:
> Input: x = 123
> Output: 321

## Example 2:
> Input: x = -123
> Output: -321

## Example 3:
> Input: x = 120
> Output: 21

## Example 4:
> Input: x = 0
> Output: 0

## Constraints:
> - -2^31 <= x <= 2^31 - 1

# Solution 1: Brute Force

## 思路

這題算是很簡單，只是由於環境不允許存 64bit，所以轉換過程中可能會 overflow，為了避免這個狀況，把原來的數字不斷除十，將每次的個位數存入 vector，之後再迴圈一個一個加乘回來。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(N)

### LeetCode Result
- Runtime: 4 ms
- Memory Usage: 6.4 MB 
- https://leetcode.com/submissions/detail/539314217/

## Code 
```cpp
class Solution {
public:
    int reverse(int x) {
        bool sign = x < 0 ? true : false;
        vector<int> reversed_int;
        x = abs(x);
        while(x > 0) {
            int tmp = x%10;
            reversed_int.emplace_back(tmp);
            x /= 10;
        }
        
        long long int sum = 0;
        for(int i = 0; i < reversed_int.size(); ++i) {
            sum += reversed_int[i] * pow(10, reversed_int.size() - i - 1);
        }

        if(sum > INT_MAX) return 0;
        if(sum < INT_MIN) return 0;
        if(sign) return sum*-1;
        return sum;
    }
};
```