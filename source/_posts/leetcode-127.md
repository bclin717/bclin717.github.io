---
title: 【LeetCode】127. Word Ladder 解題報告
categories:
  - LeetCode
date: 2022-06-14 16:20:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/3ZVzuQv.jpg
---
 
# 127. Word Ladder / Hard

A transformation sequence from word `beginWord` to word `endWord` using a dictionary wordList is a sequence of words `beginWord -> s1 -> s2 -> ... -> sk` such that:

- Every adjacent pair of words differs by a single letter.
- Every si for `1 <= i <= k` is in wordList. Note that `beginWord` does not need to be in wordList.
- `sk == endWord`

Given two words, `beginWord` and `endWord`, and a dictionary `wordList`, return the **number of words** in the **shortest transformation sequence** from `beginWord` to `endWord`, or 0 if no such sequence exists.

<!-- more --> 


## Example 1:
> Input: beginWord = "hit", endWord = "cog", wordList = ["hot","dot","dog","lot","log","cog"]
> Output: 5
> Explanation: One shortest transformation sequence is "hit" -> "hot" -> "dot" -> "dog" -> cog", which is 5 words long.

## Example 2:
> Input: beginWord = "hit", endWord = "cog", wordList = ["hot","dot","dog","lot","log"]
> Output: 0
> Explanation: The endWord "cog" is not in wordList, therefore there is no valid transformation sequence.

## Constraints: 
> - `1 <= beginWord.length <= 10`
> - `endWord.length == beginWord.length`
> - `1 <= wordList.length <= 5000`
> - `wordList[i].length == beginWord.length`
> - `beginWord`, `endWord`, and `wordList[i]` consist of lowercase English letters.
> - `beginWord != endWord`
> - All the words in wordList are **unique**.


# Solution: BFS
## 思路

這題我們必須以詞語之間的關係去思考，以範例來說，不同詞之間的關係可以畫成這張圖。
我們可以利用 BFS 來找出最短路徑。

![](https://leetcode.com/problems/word-ladder/Figures/127/Word_Ladder_1.png)


那麼如何建立出詞語之間的關係呢？
首先將各個詞做前處理，也就是依序將每個字元改為 * 替代，並放入 map 中。
舉例來說，*ot 所對應的 string vector 會存放 "hot", "pot", "rot" 等詞彙。

![](https://leetcode.com/problems/word-ladder/Figures/127/Word_Ladder_2.png)

建出這樣的 map 後，我們從 beginWord 開始，一樣先做前處理，這時就能找到下一個字，進而可以使用 BFS 來解決問題了。

例如 hit 的其中一個變形是 h*t，在 map 中會找到 hit, hot，為了避免走回頭路或是迴圈，
還需要再另外宣告一個 visited 陣列來檢查是否走過。而 hot 的變形 *ot 又可以找到 lot 與 dot。

為了知道 BFS 走了多遠，放進 queue 的不只有 string，還搭配一個 int 來記錄廣度。
一旦下一個變體的陣列中有 endWord，便直接將廣度加一後回傳。

## 效能

### Complexity 
- Time Complexity: O(M^2xN), where M is the length of each word and N is the total number of words in the input word list.
- Space Complexity: O(M^2xN)

### LeetCode Result
- Runtime: 165 ms
- Memory Usage: 33.5 MB 
- https://leetcode.com/submissions/detail/721909949/

## Code
```cpp
class Solution {
public:
    int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
        int sz = beginWord.size();
        unordered_map<string, vector<string>> hmap;
        unordered_map<string, bool> visited;
        
        for(const auto& word: wordList) {
            visited[word] = false;
            for(int i = 0; i < sz; ++i) {
                string newWord = word.substr(0, i) + "*" + word.substr(i+1, sz-i-1); 
                if(hmap.count(newWord)) {
                    hmap[newWord].emplace_back(word);
                } else {
                    hmap[newWord] = vector<string>{word};
                }
            }
        }

        queue<pair<string, int>> q;
        q.push(pair<string, int>{beginWord, 1});
        while(!q.empty()) {
            string word = q.front().first; 
            int level = q.front().second;
            q.pop();
            
            for(int i = 0; i < sz; ++i) {
                string newWord = word.substr(0, i) + "*" + word.substr(i+1, sz-i-1); 
                if(hmap.count(newWord)) {
                    for(const auto& nextWord: hmap[newWord]) {
                        if(nextWord == endWord) {
                            return level + 1;
                        } else if(!visited[nextWord]) {
                            visited[nextWord] = true;
                            q.push(pair<string, int>{nextWord, level+1});
                        }
                    }
                }
            }
        }
        return 0;
    }
};
```