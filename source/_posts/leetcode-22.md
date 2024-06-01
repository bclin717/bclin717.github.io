---
title: 【LeetCode】22. Generate Parentheses 解題報告
categories:
  - LeetCode
date: 2022-07-06 16:15:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/tEZ8j95.jpg
---
 
# 22. Generate Parentheses / Medium

Given n pairs of parentheses, write a function to generate all combinations of well-formed parentheses.

<!-- more --> 
## Example 1:
> Input: n = 3
> Output: ["((()))","(()())","(())()","()(())","()()()"]

## Example 2:
> Input: n = 1
> Output: ["()"]

## Constraints:
> - 1 <= n <= 8

# Solution: Backtracking
## 思路

無論在任何狀況下，右括弧的數量必然小於等於左括弧的數量。
我們可以用遞迴來 backtracking，終止條件則是目前造出來的字串長度已經達到 2n（左右括弧）
有兩個分支，一個是左括弧數量還沒有到 n 的時候，我們可以加左括弧，另一個是右括弧數量還少於左括弧時，可以增加右括弧。

時間複雜度的證明有點難了

## 效能

### Complexity 
- Time Complexity: : O(N*(2^2N)), you can say the time comlexity is Catalan number in interview.
- Space Complexity: O(N*(2^2N))

### LeetCode Result

- Runtime: 4 ms
- Memory Usage: 13.9 MB 
- https://leetcode.com/submissions/detail/739868906/

## Code 
```cpp
class Solution {
public:
    vector<string> generateParenthesis(int n) {
        combine("", 0, 0, n);
        return ans;
    }
    
    void combine(string nowString, int left, int right, const int& max) {
        if(nowString.size() == max*2) {
            ans.emplace_back(nowString);
            return;
        }
        
        if(left < max) {
            combine(nowString+"(", left+1, right, max);
        }
        
        if(right < left) {
            combine(nowString+")", left, right+1, max);
        }
    }
private:
    vector<string> ans;
};
```
