---
title: 【LeetCode】5. Longest Palindromic Substring 解題報告
categories:
  - LeetCode
date: 2021-07-22 18:50:00
tags: LeetCode
hidden: true
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

Manacher 的中心思想還是 EAC，只是略過一些進行過判定的回文，優化效能達到線性時間複雜度。

### 字串預處理

EAC（中心擴展法）會遇到奇數偶數的問題，我們可以透過預先處理字串來避免掉這個問題。

在每一個字元之間插入分隔符號 #，例如 babab 會變成 #b#a#b#a#d#

由於之後實行 EAC 要停止迴圈的關係，字串前後可以插入兩個不同的符號。

我們以 aabaaca 為例子：

| Idx | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 | 16 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| char | $ | # | a | # | a | # | b | # | a | # | a | # | c | # | a | # | ^ |

### P 陣列

我們利用一個 P 陣列來紀錄迴文長度，或說是擴展半徑 R。以上述範例來看，P 陣列會長的像這樣：

| Idx | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 | 16 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| char | $ | # | a | # | a | # | b | # | a | # | a | # | c | # | a | # | ^ |
| P | 0 | 0 | 1 | 2 | 1 | 0 | 5 | 0 | 1 | 2 | 1 | 0 | 3 | 0 | 1 | 0 | 0 |

### 如何計算 P？

前面提到，Manacher 還是基於 EAC 去做優化，所以計算 P 陣列的方式當然還是使用 EAC。只不過遇上一些 Case 時可以加速。在這之前，我們要先了解一下 Manacher 所使用的迴文鏡像特性。

假設我們已經計算完 P[6] = 5，設 C 為迴文中心點，R 則是迴文右邊界（R = C + P[C]）。

| Idx | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 | 16 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| char | $ | # | a | # | a | # | b | # | a | # | a | # | c | # | a | # | ^ |
| P | 0 | 0 | 1 | 2 | 1 | 0 | 5 | 0 | 1 | 2 | 1 | 0 |  |  |  |  |  |
|  |  | L |  | i' |  |  | C |  |  | i |  | R |  |  |  |  |  |

不難發現，我們可以透過 P[C]→P[L] 的值來重現 P[C]→P[R]。

也就是說 mirror_i = C - ( i - C ) 

### 鏡像的三種狀況

**Case 1:** P[i_mirror] + i < R

i 在 R 內，擴展也不會超出 R

此時可以放心地設 P[i] = P[i_mirror]

| Idx | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 | 16 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| char | $ | # | a | # | a | # | b | # | a | # | a | # | c | # | a | # | ^ |
| P | 0 | 0 | 1 | 2 | 1 | 0 | 5 | 0 | 1 | 2 | 1 | 0 |  |  |  |  |  |
|  |  | L |  | i' |  |  | C |  |  | i |  | R |  |  |  |  |  |

**Case 2:** P[i_mirror] + i > R

i 在 R 內，但擴展後超出 R

此時先把 P[i] 設成 R - i，從邊界開始進行 EAC。

| Idx | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 | 16 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| char | $ | # | a | # | a | # | c | # | a | # | a | # | c | # | a | # | ^ |
| P | 0 | 0 | 1 | 2 | 1 | 0 | 5 | 0 | 1 | 6 |  |  |  |  |  |  |  |
|  |  | L |  | i' |  |  | C |  |  | i |  | R |  |  |  |  |  |

**Case 3:** i > R

i 在 R 外，將 P[i] 設成 0，開始 EAC。

| Idx | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 | 16 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| char | $ | # | a | # | a | # | b | # | a | # | a | # | c | # | a | # | ^ |
| P | 0 | 0 | 1 | 2 | 1 | 0 | 5 | 0 | 1 | 2 | 1 | 0 |  |  |  |  |  |
|  |  | L |  |  |  |  | C |  |  |  |  | R | i |  |  |  |  |

需要注意的是，每次進行完 Case 2 與 Case 3，都要記得更新 C 和 R 的位置。

可以參考以下動畫來理解 Manacher 演算法的過程：
[Manacher's Longest Palindromic Substring Algorithm (manacher-viz.s3-website-us-east-1.amazonaws.com)](http://manacher-viz.s3-website-us-east-1.amazonaws.com/#/)

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
        string origin_s(s);
        s = insertString(s);
        const int n = s.size();
        
        vector<int> P(n, 0);
        int R = 0, C = 0, maxIdx = 0;
        int i_mirror;
        for(int i = 1; i < n-1; ++i) {
            i_mirror = C - (i - C);
            
            if(R > i && P[i_mirror] + i < R) {
                P[i] = P[i_mirror];
            } else if(R > i && P[i_mirror] + i > R) {
                P[i] = R - i;
                while(s[i + P[i] + 1] == s[i - P[i] - 1]) ++P[i];
                R = i + P[i];
                C = i;
            } else {
                P[i] = 0;
                while(s[i + P[i] + 1] == s[i - P[i] - 1]) ++P[i];
                R = i + P[i];
                C = i;
            }
            if(P[i] > P[maxIdx]) maxIdx = i;
        }
        return origin_s.substr((maxIdx - P[maxIdx] - 1) / 2, P[maxIdx]);
    }
    
    string insertString(const string& s) {
        string newStr = "$#";
        const int n = s.size();
        for(int i = 0; i < n; ++i) {
            newStr += s[i];
            newStr += "#";
        }
        newStr += "^";
        return newStr;
    }
};
```