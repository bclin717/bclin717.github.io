---
title: 【LeetCode】387. First Unique Character in a String 解題報告
categories:
  - LeetCode
date: 2022-01-10 8:15:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/yGOUXI0.jpg
---
 
# 387. First Unique Character in a String / Easy

Given a string s, find the first non-repeating character in it and return its index. If it does not exist, return -1.

<!-- more --> 

## Example 1:
> Input: s = "leetcode"
> Output: 0

## Example 2:
> Input: s = "loveleetcode"
> Output: 2

## Example 3:
> Input: s = "aabb"
> Output: -1

## Comstraints
> - 1 <= s.length <= 10^5
> - s consists of only lowercase English letters.

# Solution 1: Brute Force
## 思路

暴力解，利用雙迴圈不斷比對。用在這題不會 TLE。

## 效能

### Complexity 
- Time Complexity: O(N^2)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 55 ms
- Memory Usage: 10.6 MB 
- https://leetcode.com/submissions/detail/616578327/

## Code
```cpp
class Solution {
public:
    int firstUniqChar(string s) {
        int cnt = 0;
        for(int i = 0; i < s.size(); ++i) {
            char now_char = s[i];
            bool found = false;
            for(int j = 0; j < s.size(); ++j) {
                if(i == j) continue;
                if(now_char == s[j]) {
                    found = true;
                    break;
                } 
            }
            if(!found) return i;
        }
        return -1;
    }
};
```

# Solution 2: Hash Map
## 思路

第一次迴圈遍歷整個字串，利用 Hash Map 把每個字元的出現次數都記錄下來。
第二次迴圈再次遍歷字串，如果 Hash Map 中紀錄的 value 為 1，便回傳該字元的 index。
如果所有字元都出現兩次以上就回傳 -1。

但這題貌似用 Hash Map 卻變慢了...

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 65 ms
- Memory Usage: 10.8 MB 
- https://leetcode.com/submissions/detail/616579218/

## Code
```cpp
class Solution {
public:
    int firstUniqChar(string s) {
        unordered_map<char, int> umap;
        for(int i = 0; i < s.size(); ++i) {
            ++umap[s[i]];
        }
        
        for(int i = 0; i < s.size(); ++i) {
            if(umap[s[i]] == 1) return i;
        }
        return -1;
    }
};
```

# Solution 3: Hash Map (Array)
## 思路

和上一個解法一樣，只是換成用 Array 代替 unordered_map 實現簡單的 Hash Map，速度上快了不少。


## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 18 ms
- Memory Usage: 10.6 MB 
- https://leetcode.com/submissions/detail/616192757/

## Code
```cpp
class Solution {
public:
    int firstUniqChar(string s) {
        short hmap[26] = {0};
        const int n = s.size();
        for(const auto& c: s) ++hmap[c-'a'];
        for(int i = 0; i < n; ++i) {
            if(hmap[s[i]-'a'] == 1) return i;
        }
        return -1;
    }
};
```