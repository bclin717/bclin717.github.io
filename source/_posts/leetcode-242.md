---
title: 【LeetCode】242. Valid Anagram 解題報告
categories:
  - LeetCode
date: 2022-01-10 20:53:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/H0K4uMq.jpg
---
 
# 242. Valid Anagram / Easy

Given two strings s and t, return true if t is an anagram of s, and false otherwise.

<!-- more --> 

## Example 1:
> Input: s = "anagram", t = "nagaram"
> Output: true

## Example 2:
> Input: s = "rat", t = "car"
> Output: false


## Comstraints
> - 1 <= s.length, t.length <= 5 * 10^4
> - s and t consist of lowercase English letters.

# Solution 1: Sort
## 思路

所謂的 Anagram 指的是一樣的字元，不同的排列方式。我們可以簡單地排序兩個字串，比較它們是否為同一個字串，就知道兩者是不是 Anagram 了。

## 效能

### Complexity 
- Time Complexity: O(NlogN)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 28 ms
- Memory Usage: 7.3 MB 
- https://leetcode.com/submissions/detail/616610911/

## Code
```cpp
class Solution {
public:
    bool isAnagram(string s, string t) {
        sort(s.begin(), s.end());
        sort(t.begin(), t.end());
        return s == t;
    }
};
```


# Solution 2: Hash Map
## 思路

排序很花時間，不如用 Hash Map 把每個字元出現的次數記錄下來，如果兩字串的每一個字元出現次數皆相同，即為 Anagram。時間複雜度因為 Hash Map 降到 O(N)，缺點是空間複雜度比起排序會上升。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 0 ms
- Memory Usage: 7.3 MB 
- https://leetcode.com/submissions/detail/616216868/

## Code
```cpp
class Solution {
public:
    bool isAnagram(string s, string t) {
        short hmap[26] = {0};
        for(const auto& c: s) ++hmap[c-'a'];
        for(const auto& c: t) --hmap[c-'a'];
        for(const auto& num: hmap) if(num != 0) return false;
        return true;
    }
};
```