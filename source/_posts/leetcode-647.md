---
title: 【LeetCode】647. Palindromic Substrings 解題報告
categories:
  - LeetCode
date: 2021-06-17 21:55:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/BVRwFnV.jpg
---
# 647. Palindromic Substrings / Medium

Given a string s, return the number of palindromic substrings in it.

A string is a palindrome when it reads the same backward as forward.

A substring is a contiguous sequence of characters within the string.

<!-- more -->

## Example 1:
> Input: s = "abc"
> Output: 3
> Explanation: Three palindromic strings: "a", "b", "c".

## Example 2:
> Input: s = "aaa"
> Output: 6
> Explanation: Six palindromic strings: "a", "a", "a", "aa", "aa", "aaa".

## Constraints:
> - 1 <= s.length <= 1000
> - s consists of lowercase English letters.

# Solution 1: DP
## 思路

開一個 2-D vector 用來存 DP 狀態，dp[i][j] 代表子字串 s[i] ~ s[j] 是否是迴文。

迴文 (dp[i][j] == true) 的條件： 
1. (s[i] == s[j]) && (j-i < 2) 
      - 可能是只有一個字元，如 a 
      - 兩個字元，如 aa
2. (s[i] == s[j]) && (dp[i+1][j-1] == true)
      - 組成迴文的條件，最左和最右的字元相等，而且去掉最邊邊的兩個字元後，也是迴文。 

## 效能

### Complexity 
- Time Complexity: O(N^2)
- Space Complexity: O(N^2)

### LeetCode Result

- Runtime: 12 ms
- Memory Usage: 7.6 MB 
- https://leetcode.com/submissions/detail/509257996/

## Code 
```cpp
class Solution {
public:
    int countSubstrings(string s) {
        int n = s.size();
        int ans = 0;
        vector<vector<bool>> dp(n, vector<bool>(n, false));

        for(int i = n-1; i >= 0; --i) {
            for(int j = i; j < n; ++j) {
                if(s[i] == s[j] && (j-i < 2 || dp[i+1][j-1])) {
                    dp[i][j] = true;
                    ++ans;
                }
            }
        }
        
        return ans;      
    }
};
```
# Solution 2: Brute Force
## 思路

暴力解，遍歷每一個字元，每次都以其作為中點向左右展開判斷是否還是迴文，是就加一。看程式碼就很直白了。

## 效能

### Complexity 
- Time Complexity: O(N^2)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 0 ms
- Memory Usage: 13.3 MB 
- https://leetcode.com/submissions/detail/509644577/

## Code 
```cpp
class Solution {
public:
    int ans = 0;
    int countSubstrings(string s) {
        int size = s.size();
        for(int i = 0; i < size; ++i) {
            count(s, i, i);
            count(s, i, i+1);
        }
        return ans;
    }
    
    void count(string s, int i, int j) {
        int size = s.size();
        while(i >= 0 && j < size && s[i] == s[j]) {
            ++ans;
            --i;
            ++j;
        }
    }
};
```

# Solution 3: Manacher Algorithm
## 思路

著名的 Manacher （馬拉車）算法，只要線性時間就可以找出所有迴文，主要用於 LPS （Longest Palindromic Substring）問題。

但我自己每次都會忘記，網路上已經有很多關於 Manacher 的教學，就不多作解釋了。我推薦這篇：https://havincy.github.io/blog/post/ManacherAlgorithm/

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 8 ms
- Memory Usage: 6.9 MB 
- https://leetcode.com/submissions/detail/427086355/

## Code 
```cpp
class Solution {
public:
    int countSubstrings(string s) {
        s = insertSymbol(s);
        int n = s.size();
        int radius[3000] = {0};
        int C = 0, R = 0;
        
        for(int i = 1; i < n-1; ++i) {
            int mirror_i = C*2 - i;
            
            if(mirror_i >= 1 && i < R && (i + radius[mirror_i] < R)) {
                radius[i] = radius[mirror_i];
                continue;
            }
            
            while(s[i-1-radius[i]] == s[i+1+radius[i]]) {       
                ++radius[i];
            }
            
            if(i+radius[i] > R) {
                C = i;
                R = i + radius[i];
            }
        }
        
        int count = 0;
        for(int i = 0; i < n; ++i) {
            count += (radius[i]+1)/2;
        }
        
        return count;
    }
    
    string insertSymbol(string s) {
        string symbolString {"$#"};
        for(int i = 0; i < s.size(); ++i) {
            symbolString += s[i];
            symbolString += "#";
        }
        return symbolString + "^";
    }
};
```