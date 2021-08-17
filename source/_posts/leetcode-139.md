---
title: 【LeetCode】139. Word Break 解題報告
categories:
  - LeetCode
date: 2021-06-21 17:55:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/BFYTsA1.jpg
---
 
# 139. Word Break / Medium

Given a string s and a dictionary of strings wordDict, return true if s can be segmented into a space-separated sequence of one or more dictionary words.

Note that the same word in the dictionary may be reused multiple times in the segmentation.

<!-- more --> 

## Example 1:
> Input: s = "leetcode", wordDict = ["leet","code"]
> Output: true
> Explanation: Return true because "leetcode" can be segmented as "leet code".

## Example 2:
> Input: s = "applepenapple", wordDict = ["apple","pen"]
> Output: true
> Explanation: Return true because "applepenapple" can be segmented as "apple pen apple".
> Note that you are allowed to reuse a dictionary word.

## Example 3:
> Input: s = "catsandog", wordDict = ["cats","dog","sand","and","cat"]
> Output: false

## Constraints:
> - 1 <= s.length <= 300
> - 1 <= wordDict.length <= 1000
> - 1 <= wordDict[i].length <= 20
> - s and wordDict[i] consist of only lowercase English letters.
> - All the strings of wordDict are unique.

# Solution: DP with HashMap
## 思路

這題解法很多，網路上也有 Set 解、暴力解和其他解法[1][2]，我則是用個人認為比較優雅的解法：DP + HashMap。

建立 HashMap，Key 是開頭字母，Value 則是一個 string vector，裡面存所有此字元開頭的字串。

開一個 bool vector 用來作為 dp 的紀錄，dp[i] 代表用 wordDict 內的 string 去比對，是否能到第 i 個字元。

迴圈遍歷整個字串，以 example 1 為例，最一開始的字元是 'l'，在 HashMap 中會拿出 'leet'，這時我們用 substr 去切，從目前的 i (0) 開始切 'leet' 的長度 (4)，然後將這個從 s 切出來的字串 (leet) 與 HashMap 抓出來的字串 (leet) 比對，假使兩字串相同 並且 dp[i] 為真，表示 wordDict 中的字串可以組合出一個字串和 s.substr(0, 4) 相同，dp[4] 因此為真。

最後只要看 dp vector 的最後一項是不是為真，就知道 wordDict 中的字串是否可以組合出字串完整的 s。

## 效能

### Complexity 
- Time Complexity: O(MN)
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 0 ms
- Memory Usage: 7.7 MB 
- https://leetcode.com/submissions/detail/511033584/

## Code 
```cpp
class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        auto size = s.size();
        unordered_map<char, vector<string>> dict;
        for(auto& word: wordDict) {
            dict[word[0]].emplace_back(word);
        }
        
        vector<bool> dp(size + 1, false);
        dp[0] = true;
        
        for(int i = 0; i <= size; ++i) {
            for(auto& element: dict[s[i]]) {
                auto e_size = element.size();
                if(dp[i] && element == s.substr(i, e_size)) 
                    dp[i + e_size] = true;
            }
        }
    
        return dp[size];
    }
};
```


# Reference
[1] https://www.cnblogs.com/grandyang/p/4257740.html
[2] https://zxi.mytechroad.com/blog/leetcode/leetcode-139-word-break/