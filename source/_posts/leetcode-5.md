---
title: 【LeetCode】5. Longest Palindromic Substring 解題報告
categories:
  - LeetCode
date: 2021-07-22 18:50:00
tags: LeetCode
photos:
    - https://i.imgur.com/FFKTLD3.jpg
---

# 5. Longest Palindromic Substring / Medium

Given a string s, return the longest palindromic substring in s.

<!-- more -->

## Example 1:
> Input: s = "babad"
> Output: "bab"
> Note: "aba" is also a valid answer.

## Example 2:
> Input: s = "cbbd"
> Output: "bb"

## Example 3:
> Input: s = "a"
> Output: "a"

## Example 4:
> Input: s = "ac"
> Output: "a"

## Constraints:
> 1 <= s.length <= 1000
> s consist of only digits and English letters (lower-case and/or upper-case),


# Solution 1: Expand Around Center

## 思路

中心擴展法很直覺，就是把每一個字元都當作是中心，用一個迴圈檢查左右是否相同，若不相同或是觸及邊界就跳出迴圈，記下最長的長度以及開頭，最後再回傳對應的子字串。

最差的狀況是每一個字元都相同，每一次中心展開都要展開到邊界才會停下來，即 O(N^2)；如果都不同，那就接近O(N)。

需要注意的是字串長度為奇數和偶數的狀況不同，所以才會作兩次擴展。

## 效能

### Complexity 
- Time Complexity: O(N^2)
- Space Complexity: O(1)

### LeetCode Result
- Runtime: 12 ms
- Memory Usage: 7.1MB
- https://leetcode.com/submissions/detail/525373538/

## Code 
```cpp
class Solution {
public:
    int start = 0;
    int maxL = 0;
    int n = 0;
    string longestPalindrome(string s) {
        n = s.size();
        if(n == 1) return s;
        for(int i = 0; i < n-1; ++i) {
            search(s, i, i);
            search(s, i, i+1);
        }
        return s.substr(start, maxL);
    }
    
    void search(const string& s, int left, int right) {
        while(left >= 0 && right < n && s[left] == s[right]) --left, ++right;
        if(right-left-1 > maxL) {
            maxL = right-left-1;
            start = left+1;
        }
    }
};
```

# Solution 2: DP

## 思路

DP 應該也算是很直覺，開一個陣列 dp，dp[i][j] 代表子字串s[i, j]是否為迴文。
dp[i][j] 為迴文的條件，即 dp[i+1][j-1] 也是迴文，同時 s[i] == s[j]。

以範例的程式碼來說，i 是右邊界，j 是左邊界。

然而，這題用 DP 除了需要花雙迴圈 O(N^2) 的時間複雜度，空間上也需要一個二維陣列，也是 O(N^2)，比中心擴展法還慢又佔空間，恰驗證了 DP 不是每個狀況下都適用。

## 效能

### Complexity 
- Time Complexity: O(N^2)
- Space Complexity: O(N^2)

### LeetCode Result
- Runtime: 620 ms
- Memory Usage: 30.2 MB
- https://leetcode.com/submissions/detail/526491058/

## Code 
```cpp
class Solution {
public:
    string longestPalindrome(string s) {
        int n = s.size();
        int maxL = 0, start = 0;
        if(n == 1) return s;
        vector<vector<bool>> dp(n, vector<bool>(n,true)); 
        for (int i = n-1; i >= 0; --i) {
            for (int j = i; j < n; ++j) {
                if (i < j && s[i] == s[j]) dp[i][j] = dp[i+1][j-1];
                if (s[i] != s[j]) dp[i][j] = false;
                if (dp[i][j] && j-i+1 > maxL) {
                    maxL = j-i+1;
                    start = i;
                }
            }
        }        
        return s.assign(s, start, maxL);
    }
};
```

# Solution 3: Manacher

## 思路

大名鼎鼎的 Manacher（馬拉車）算法，只要線性時間就可以找出所有迴文，主要用於 LPS （Longest Palindromic Substring）問題。

我自己每次都會忘記，網路上已經有很多關於 Manacher 的教學，就不多作解釋了。我推薦這篇：https://havincy.github.io/blog/post/ManacherAlgorithm/

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(N)

### LeetCode Result
- Runtime: 4 ms
- Memory Usage: 9.2 MB
- https://leetcode.com/submissions/detail/525905492/

## Code 
```cpp
class Solution {
public:
    string longestPalindrome(string s) {
        int n = s.size();
        if(n == 1) return s;
        string T;
        
        T += "$#";
        for(int i = 0; i < n; ++i) {
            T += s[i];
            T += "#";
        }
        T += "^";
        n = T.size();
        
        vector<int> P(n, 0);
        int R = 0, C = 0, maxIdx = 0;
        int i_mirror;
        for(int i = 1; i < n-1; ++i) {
            i_mirror = C - (i - C);
            P[i] = 0; // Case 3
            if(R > i && P[i_mirror] + i < R) { // Case 1
                P[i] = P[i_mirror];
            } else {
                if(R > i) 
                    P[i] = R - i; // Case 2
                while(T[i + P[i] + 1] == T[i - P[i] - 1]) 
                    ++P[i];
                R = i + P[i];
                C = i;
            }
            if(P[i] > P[maxIdx]) 
                maxIdx = i;
        }
        return s.substr((maxIdx-1-P[maxIdx]) / 2, P[maxIdx]);
    }
```