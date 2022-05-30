---
title: 【LeetCode】227. Basic Calculator II 解題報告
categories:
  - LeetCode
date: 2022-05-30 21:56:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/fqFVxmo.jpg
---
 
# 227. Basic Calculator II / Medium

Given a string s which represents an expression, evaluate this expression and return its value. 

The integer division should truncate toward zero.

You may assume that the given expression is always valid. All intermediate results will be in the range of [-231, 231 - 1].

Note: You are not allowed to use any built-in function which evaluates strings as mathematical expressions, such as eval().


<!-- more --> 

## Example 1:
> Input: s = "3+2*2"
> Output: 7

## Example 2:
> Input: s = " 3/2 "
> Output: 1

## Example 3:
> Input: s = " 3+5 / 2 "
> Output: 5
 

## Constraints: 
> - 1 <= s.length <= 3 * 10^5
> - s consists of integers and operators ('+', '-', '*', '/') separated by some number of spaces.
> - s represents a valid expression.
> - All the integers in the expression are non-negative integers in the range [0, 2^31 - 1].
> - The answer is guaranteed to fit in a 32-bit integer.


# Solution 1: Stack
## 思路

計算機是很經典的題目，在上資料結構時很常以計算機作為例子。
基本上可以利用 Stack 來完成。

我們先用一個迴圈將字元一個一個解析，如果是數字，那就不斷乘十加總起來，直到遇到運算子 或是 最後一個字元為止。
由於運算元有優先序，所以遇到 + 和 - 的時候，我們就把數字放進堆疊；遇到 * 和 / 的時候，就把數字和堆疊最頂的數字相互運算，再放回堆疊。
最後，將整個堆疊的數字加總起來，便是答案。

需要注意的是，由於加減的時候，數字是有正負之分的，所以我們是紀錄數字前面的運算子，而不是後面的。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 12 ms
- Memory Usage: 8.6 MB 
- https://leetcode.com/submissions/detail/545012555/

## Code
```cpp
class Solution {
public:
    int calculate(string s) {
        stack<int> nums;
        int currentNumber = 0;
        char operation = '+';
        for(auto&& it = s.begin(); it != s.end(); ++it) {
            char c = *it;
            if(isdigit(c)) {
                currentNumber = (currentNumber * 10) + (c - '0');
            } 
            
            if(c == '+' || c == '-' || c == '*' || c == '/' || it == s.end()-1) {
                if(operation == '+') {
                    nums.push(currentNumber);
                } else if(operation == '-') {
                    nums.push(-currentNumber);
                } else if(operation == '*') {
                    currentNumber *= nums.top(); 
                    nums.pop();
                    nums.push(currentNumber);
                } else if(operation == '/') {
                    currentNumber = nums.top() / currentNumber; 
                    nums.pop();
                    nums.push(currentNumber);
                }
                operation = c;
                currentNumber = 0;
            }
        }
        
        int sum = 0;
        while(!nums.empty()) {
            sum += nums.top();
            nums.pop();
        }
        return sum;
    }
};
```

# Solution 2: Optimised Approach without the stack
## 思路

那能不能不用堆疊來解這題呢？
仔細觀察的話，就會發現，除了堆疊頂端的數字，剩餘的其實可以先加起來。
於是我們捨棄堆疊，改宣告一個變數 sum 來記錄這些已經不需要運算的數字。
原先堆疊頂端的數字，我們用 lastNumber 來記錄，而 currentNumber 則是目前的數字。
如此一來，我們就可以在不需要堆疊的情況下，將運算式解出。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 0 ms
- Memory Usage: 7.9 MB 
- https://leetcode.com/submissions/detail/710488698/

## Code
```cpp
class Solution {
public:
    int calculate(string s) {
        int currentNumber = 0;
        int lastNumber = 0;
        int sum = 0;
        char operation = '+';
        unordered_set<char> operations = {'+', '-', '*', '/'};
        
        for(const auto& c: s) {
            if(isdigit(c)) {
                currentNumber = (currentNumber*10) + (c-'0');
            }
            
            if(operations.count(c) || &c == &s.back()) {
                if(operation == '+' || operation == '-') {
                    sum += lastNumber;
                    lastNumber = operation == '-' ? -currentNumber : currentNumber;
                } else if(operation == '*') {
                    lastNumber *= currentNumber;
                } else if(operation == '/') {
                    lastNumber /= currentNumber;
                }
                operation = c;
                currentNumber = 0;
            }
        }
        sum += lastNumber;
        return sum;
    }
};
```

