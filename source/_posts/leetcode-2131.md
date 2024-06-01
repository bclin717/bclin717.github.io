---
title: 【LeetCode】2131. Longest Palindrome by Concatenating Two Letter Words 解題報告
categories:
  - LeetCode
date: 2022-08-17 15:48:00
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/pYC60DP.png
---
 
# 2131. Longest Palindrome by Concatenating Two Letter Words / Medium

You are given an array of strings words. Each element of words consists of two lowercase English letters.

Create the longest possible palindrome by selecting some elements from words and concatenating them in any order. Each element can be selected at most once.

Return the length of the longest palindrome that you can create. If it is impossible to create any palindrome, return 0.

A palindrome is a string that reads the same forward and backward.

<!-- more --> 

## Example 1:
> Input: words = ["lc","cl","gg"]
> Output: 6
> Explanation: One longest palindrome is "lc" + "gg" + "cl" = "lcggcl", of length 6.
> Note that "clgglc" is another longest palindrome that can be created.

## Example 2:
> Input: words = ["ab","ty","yt","lc","cl","ab"]
> Output: 8
> Explanation: One longest palindrome is "ty" + "lc" + "cl" + "yt" = "tylcclyt", of length 8.
> Note that "lcyttycl" is another longest palindrome that can be created.

## Example 3:
> Input: words = ["cc","ll","xx"]
> Output: 2
> Explanation: One longest palindrome is "cc", of length 2.
> Note that "ll" is another longest palindrome that can be created, and so is "xx".

## Comstraints
> - 1 <= words.length <= 10^5
> - words[i].length == 2
> - words[i] consists of lowercase English letters.

# Solution 1: HashMap
## 思路

我們先利用 hashmap 來存每一個 word 出現的次數。

接著思考一下 Palindrome 的判斷標準，

1. 如果遇到兩個字元是相等的，如 "aa"

這種情況下我們需要判斷，"aa"剩餘出現的次數是不是大於一次，如果是的話，就兩組配一起，
然後從 hashmap 裏扣掉兩次，接著將答案加四。

如果只有一次，代表是落單的，用一個 flag 記下來。最後如果 flag 是 true 就加二。也就是加在最後答案的中間。
例如目前我們有 "acca"，最後判斷的時候發現其實有 "aa"，我們就可以把 "aa" 當作中心，變成 "acaaca"。

2. 如果兩次字元不同，如 "ab"

首先把這個字元倒過來，找看看 hashmap 裡面有沒有 "ba"，如果有的話就把兩者從 hashmap 中減一，答案加四。


## 效能

### Complexity 
- Time Complexity: O(N), where N is the length of words
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 771 ms
- Memory Usage: 167.9 MB 
- https://leetcode.com/submissions/detail/775295489/

## Code
```cpp
class Solution {
public:
    int longestPalindrome(vector<string>& words) {
        bool found = false;
        unordered_map<string, int> hmap;
        for(const auto& word: words) ++hmap[word];
        
        int res = 0;
        for(auto word: words) {
            if(word[0] == word[1]) {
                if(hmap[word] > 1) {
                    hmap[word] -= 2;
                    res += 4;
                } else if(hmap[word] == 1) {
                    found = true;
                }
            } else {
                string rev = word;
                reverse(rev.begin(), rev.end());
                if(hmap[word] > 0 && hmap[rev] > 0) {
                    res += 4;
                    --hmap[rev];
                    --hmap[word];
                } 
            }
        }
        
        if(found) res += 2;
        return res;
    }
};
```

# Solution 2: Array
## 思路

我們可以用一個二維陣列來取代 hashmap，
counter[a][b] 代表 a 字元與 b 字元出現的次數。
如果目前拿到的是 ab 字元，我們就去找 counter[b][a] 有沒有存在，有的話就將其減一，答案加四。
沒有的話，就把 counter[a][b] 加一。

最後再檢查有沒有兩個字元相同（如 "aa"）的字串存在，也就是檢查 counter[0][0], counter[1][1] ... counter[25][25] 是否有大於零。
如果有，就把答案加二，跳出迴圈。

## 效能

### Complexity 
- Time Complexity: O(N), where N is the length of words
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 275 ms
- Memory Usage: 167.7 MB 
- https://leetcode.com/submissions/detail/775842178/

## Code
```cpp
class Solution {
public:
    int longestPalindrome(vector<string>& words) {
        int res = 0;
        int counter[26][26] = {0};
        for(auto& word: words) {
            char a = word[0] - 'a', b = word[1] - 'a';
            if(counter[b][a]) {
                --counter[b][a]; 
                res += 4;
            } else {
                ++counter[a][b];
            }
        }
        
        for(int i = 0; i < 26; ++i) {
            if(counter[i][i] > 0) {
                res += 2;
                break;
            }
        }
        return res;
    }
};
```