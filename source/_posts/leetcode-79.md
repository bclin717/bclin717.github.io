---
title: 【LeetCode】79. Word Search 解題報告
categories:
  - LeetCode
date: 2022-07-07 14:46:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/DhHv4LR.jpg
---
 
# 79. Word Search / Medium

Given an `m x n` grid of characters `board` and a string `word`, return `true` if `word` exists in the grid.

The word can be constructed from letters of sequentially adjacent cells, where adjacent cells are horizontally or vertically neighboring. The same letter cell may not be used more than once.

<!-- more --> 

## Example 1:
![](https://assets.leetcode.com/uploads/2020/11/04/word2.jpg)
> Input: board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCCED"
> Output: true

## Example 2:
![](https://assets.leetcode.com/uploads/2020/11/04/word-1.jpg)
> Input: board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "SEE"
> Output: true

## Example 3:
![](https://assets.leetcode.com/uploads/2020/10/15/word3.jpg)
> Input: board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCB"
> Output: false

## Constraints: 
> - m == board.length
> - n = board[i].length
> - 1 <= m, n <= 6
> - 1 <= word.length <= 15
> - board and word consists of only lowercase and uppercase English letters.

- Follow up: Could you use search pruning to make your solution faster with a larger board?

# Solution 1: Backtracking
## 思路 

很直覺地用 DFS 做 Backtracking。

## 效能

### Complexity 
- Time Complexity: O(N*3^L), 3 is the direction every time it takes, where N is the number of cells and L is the length of the word.
- Space Complexity: O(L)

### LeetCode Result

- Runtime: 259 ms
- Memory Usage: 8.1 MB 
- https://leetcode.com/submissions/detail/731609728/
![](https://i.imgur.com/bW6s71g.png)

## Code
```cpp
class Solution {
public:
    bool exist(vector<vector<char>>& board, string word) {
        m = board.size();
        n = board[0].size();
        vector<vector<bool>> visited(m, vector<bool>(n, false));
        
        for(int i = 0; i < m; ++i) {
            for(int j = 0; j < n; ++j) {
                if(dfs(board, i, j, move(word), 0, visited)) {
                    return true;
                }
            }
        }
        return false;
    }
    
    bool dfs(vector<vector<char>>& board, int x, int y, const string& word, int step, vector<vector<bool>>& visited) {
        if(x < 0 || x >= m || y < 0 || y >= n || visited[x][y] || board[x][y] != word[step]) 
            return false;
        
        if(step == word.size()-1) return true;
        
        visited[x][y] = true;
        bool flag = dfs(board, x+1, y, word, step+1, visited) ||
                   dfs(board, x-1, y, word, step+1, visited) ||
                   dfs(board, x, y+1, word, step+1, visited) ||
                   dfs(board, x, y-1, word, step+1, visited);
        visited[x][y] = false;
        return flag;
    }
private:
    int m, n;
};
```

# Solution 2: Backtracking + Pruning
## 思路 

要加速的話只能做 Pruning（剪枝），也就是在 DFS 的決策樹上，其實我們並不需要搜尋每一個可能，有一些可以決定不搜尋，而決定的過程就叫做 Pruning。
以這題來說有三個 Pruning 的方式：
1. 字串 word 長度大於 board 大小。
這種狀況下就沒辦法找到這條路了，可以直接 return false
2. board 上缺少了 word 中的某個字元。
這種狀況下也沒辦法找到，可以直接 return false
3. Prefix 與 Suffix 的長度。
設想一種狀況，我們拿到了一個字串是 aaaaaaaaaab，拿到的盤面也很大，並且都是 a，而 b 則是在最右下角。
這種狀態下，若我們按順序 DFS，在初期都會遭遇 aaaaaaaaaaa，所以會花非常多的時間在 DFS 沒有意義的區域。
為了解決這個狀況，我們事先判斷 word 的 prefix 和 suffix 的長度，如果 prefix > suffix，我們就反轉字串。
這樣使得我們可以減少進入 DFS 的可能性。以上面的範例來看，prefix 是 10 而 suffix 是 1，反轉後就會變成 baaaaaaaaaa。

只要做完前兩種 Pruning，就可以把這題的運行時間從 270ms 降到約 75ms，而第三種則直接減至 0ms。

## 效能

### Complexity 
- Time Complexity: O(N*3^L), 3 is the direction every time it takes, where N is the number of cells and L is the length of the word.
- Space Complexity: O(L)

### LeetCode Result

- Runtime: 0 ms
- Memory Usage: 8 MB 
- https://leetcode.com/submissions/detail/740708861/
![](https://i.imgur.com/6MjhKKb.png)

## Code
```cpp
class Solution {
public:
    bool exist(vector<vector<char>>& board, string word) {
        m = board.size();
        n = board[0].size();
        vector<vector<bool>> visited(m, vector<bool>(n, false));
        
        // Prune #1: If there aren't even enough letters in the board to fit word, then return false
        if(word.size() > m*n) return false;
        
        // Prune #2: For each letter, the board must contain at least as many of that letter as word contains
        int hmap[58] = {};
        for(const auto& v: board) 
            for(const auto& ch: v) 
                ++hmap[ch - 'A'];
        for(auto& ch: word) if(--hmap[ch - 'A'] < 0) return false; 
        
        // Prune #3: Find the longest prefix/suffix of the same character. If the longest suffix is longer
        // than the longest prefix, swap the strigns (so we are less likely to have a long prefix with a lot
        // of the same character)
        int left_pref = word.find_first_not_of(word[0]);
        int right_pref = word.size() - word.find_last_not_of(word.back());
        if(left_pref > right_pref) reverse(word.begin(), word.end());
        
        // DFS starts here
        for(int i = 0; i < m; ++i) {
            for(int j = 0; j < n; ++j) {
                if(dfs(board, i, j, move(word), 0, visited)) {
                    return true;
                }
            }
        }
        return false;
    }
    
    bool dfs(vector<vector<char>>& board, int x, int y, const string& word, int step, vector<vector<bool>>& visited) {
        if(x < 0 || x >= m || y < 0 || y >= n || visited[x][y] || board[x][y] != word[step]) 
            return false;
        
        if(step == word.size()-1) return true;
        
        visited[x][y] = true;
        bool flag = dfs(board, x+1, y, word, step+1, visited) ||
                   dfs(board, x-1, y, word, step+1, visited) ||
                   dfs(board, x, y+1, word, step+1, visited) ||
                   dfs(board, x, y-1, word, step+1, visited);
        visited[x][y] = false;
        return flag;
    }
private:
    int m, n;
};
```