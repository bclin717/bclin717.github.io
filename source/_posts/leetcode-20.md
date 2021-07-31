---
title: 【LeetCode】20. Valid Parentheses 解題報告
categories:
  - LeetCode
date: 2021-07-31 23:43:56
tags: LeetCode
photos:
    - https://i.imgur.com/nrSTM12.jpg
---
 
# 20. Valid Parentheses / Easy

Given a string s containing just the characters '(', ')', '{', '}', '[' and ']', determine if the input string is valid.

An input string is valid if: \
    1. Open brackets must be closed by the same type of brackets.\
    2. Open brackets must be closed in the correct order.

<!-- more --> 

## Example 1:
> Input: s = "()"
> Output: true

## Example 2:
> Input: s = "()[]{}"
> Output: true

## Example 3:
> Input: s = "(]"
> Output: false

## Example 4:
> Input: s = "([)]"
> Output: false

## Example 5:
> Input: s = "{[]}"
> Output: true

# Solution: Stack
## 思路

這題是很典型的堆疊題，利用 Stack 先進後出的特性就能很輕易地判斷括弧是否 valid。

一開始先檢查字串的長度是不是偶數，不是的話，括弧必定 invalid。
接著，如果是左括弧，就放入堆疊；右括弧則把堆疊最上方的元素拿來比較，如果是同一種括弧，就把元素 pop 出去，反之則回傳 false。

最後檢查堆疊是不是空的，若不是就回傳 false，例如 "((" 這類只有左括弧的字串便會出現這種狀況。

至於判斷括弧對應的方式有很多種，可以用 if-else 或 switch 寫死，也可以用 map，我兩種都有寫，在這題對效能影響不大，記憶體用量則有輕微影響。


## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 0 ms
- Memory Usage: 6.2 MB 
- https://leetcode.com/submissions/detail/531107369/

## Code 1
```cpp
class Solution {
public:
    bool isValid(string s) {
        if(s.size() % 2 == 1) return false;
        stack<char> sk;
        for(char c : s) {
            if(c == '(' || c == '[' || c == '{') {
                sk.push(c);
            } else {
                if(sk.empty()) return false;
                if(sk.top() == '(' && c != ')') return false;
                if(sk.top() == '[' && c != ']') return false;
                if(sk.top() == '{' && c != '}') return false;
                sk.pop();
            }
        }
        if(!sk.empty()) return false;
        return true;
    }
};
```

## Code 2: With Map
```cpp
class Solution {
public:
    bool isValid(string s) {
        if(s.size() % 2 == 1) return false;
        stack<char> sk;
        map<char, char> m {
            {'(', ')'},
            {'[', ']'}, 
            {'{', '}'}
        };
        
        for(char c : s) {
            if(c == '(' || c == '{' || c == '[') {
                sk.push(c);
            } else {
                if(sk.empty() || m[sk.top()] != c) {
                    return false;
                } else {
                    sk.pop();
                }
            }
        }
        if(!sk.empty()) return false;
        return true;
    }
};
```
