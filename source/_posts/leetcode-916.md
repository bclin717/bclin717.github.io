---
title: 【LeetCode】916. Word Subsets 解題報告
categories:
  - LeetCode
date: 2021-08-01 13:51:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/7sBoHut.jpg
---
 
# 916. Word Subsets / Medium
You are given two string arrays `words1` and `words2`.

A string b is a **subset** of string a if every letter in b occurs in a including multiplicity.

For example, `"wrr"` is a subset of `"warrior"` but is not a subset of `"world"`.
A string a from words1 is **universal** if for every string b in words2, b is a subset of a.

Return an array of all the **universal** strings in words1. You may return the answer in **any order**.

<!-- more --> 
 

## Example 1:
> Input: words1 = ["amazon","apple","facebook","google","leetcode"], words2 = ["e","o"]
> Output: ["facebook","google","leetcode"]

## Example 2:
> Input: words1 = ["amazon","apple","facebook","google","leetcode"], words2 = ["l","e"]
> Output: ["apple","google","leetcode"]

## Constraints: 
> - 1 <= words1.length, words2.length <= 10^4
> - 1 <= words1[i].length, words2[i].length <= 10
> - words1[i] and words2[i] consist only of lowercase English letters.
> - All the strings of `words1` are **unique**.

# Solution: Reduce to Single Word in B
## 思路 

這題看起來很複雜，一個一個暴力比對會超時，所以我們先把 words2 裡面的字串的字串都 reduce 成一個。
例如 "arr" + "row" 就可以被 reduce 成 "arrow"。我們用一個陣列作為 map 來儲存。
之後就是一個一個從 words1 中拿出字串來比對，如果通過比對就放入答案的陣列，最後回傳。

## 效能

### Complexity 
- Time Complexity: O(A+B), where A and B is the information of word1 and words2. 
- Space Complexity: O(A.len + B.en)

### LeetCode Result

- Runtime: 20 ms
- Memory Usage: 17.5 MB 
- https://leetcode.com/submissions/detail/760363095/

## Code
```cpp
class Solution {
public:
    vector<string> wordSubsets(vector<string>& words1, vector<string>& words2) {
        vector<string> res;
        int i = 0;
        vector<int> count2(26, 0);
        for(auto& word: words2) {
            auto tmp = counter(word);
            for(int i = 0; i < 26; ++i) {
                count2[i] = max(count2[i], tmp[i]);
            } 
        }
        
        for(auto& word: words1) {
            auto tmp = counter(word);
            for(i = 0; i < 26; ++i) {
                if(tmp[i] < count2[i]) {
                    break;
                }
            }
            if(i == 26) res.emplace_back(word);
        }
        return res;
    }
    
    vector<int> counter(string word) {
        vector<int> res(26, 0);
        for(auto& c: word) {
            res[c-'a']++;
        }
        return res;
    }
};
```