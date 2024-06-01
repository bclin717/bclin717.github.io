---
title: 【LeetCode】17. Letter Combinations of a Phone Number 解題報告
categories:
  - LeetCode
date: 2022-07-06 15:10:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/6gfDWzO.jpg
---
 
# 17. Letter Combinations of a Phone Number / Medium

Given a string containing digits from 2-9 inclusive, return all possible letter combinations that the number could represent. Return the answer in any order.

A mapping of digits to letters (just like on the telephone buttons) is given below. Note that 1 does not map to any letters.

![](https://assets.leetcode.com/uploads/2022/03/15/1200px-telephone-keypad2svg.png)

<!-- more --> 
## Example 1:
> Input: digits = "23"
> Output: ["ad","ae","af","bd","be","bf","cd","ce","cf"]

## Example 2:
> Input: digits = ""
> Output: []

## Example 3:
> Input: digits = "2"
> Output: ["a","b","c"]
 

## Constraints:
> - 0 <= digits.length <= 4
> - `digits[i]` is a digit in the range `['2', '9']`.

# Solution: Backtracking
## 思路

## 效能

### Complexity 
- Time Complexity: O(N*4^N), where N is the length of digits, and 4 is the maximum value length in the hash map
- Space Complexity: O(N), the space occupied by the recursion call stack will only go as deep as the number of digits in the input.

### LeetCode Result

- Runtime: 0 ms
- Memory Usage: 6.7 MB 
- https://leetcode.com/submissions/detail/729736333/

## Code 
```cpp
class Solution {
public:
    vector<string> letterCombinations(string digits) {
        if(digits.empty()) return {};
        combination(digits, "");
        return output;
    }
    
    void combination(string nowDigits, string nowCombination) {
        if(nowDigits.empty()) {
            output.emplace_back(nowCombination);
            return;
        }
        string charCandidate = hmap[nowDigits[0]];
        for(const auto& cans: charCandidate) {
            nowCombination += cans;
            combination(string(nowDigits.begin()+1, nowDigits.end()), nowCombination);
            nowCombination.pop_back();
        }
    }
private:
    vector<string> output;
    unordered_map<char, string> hmap = {
        {'2', "abc"},
        {'3', "def"},
        {'4', "ghi"},
        {'5', "jkl"},
        {'6', "mno"},
        {'7', "pqrs"},
        {'8', "tuv"},
        {'9', "wxyz"},
    };   
};
```
