---
title: 【LeetCode】20. Valid Parentheses 解題報告
categories:
  - LeetCode
date: 2021-07-30 22:02:56
tags: LeetCode
photos:
    - https://i.imgur.com/nrSTM12.jpg
---
 
# 20. Valid Parentheses / Medium

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
