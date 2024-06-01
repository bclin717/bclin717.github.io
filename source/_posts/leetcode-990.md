---
title: 【LeetCode】990. Satisfiability of Equality Equations 解題報告
categories:
  - LeetCode
date: 2022-09-27 10:51:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/opzBDTj.jpg
---
 
# 990. Satisfiability of Equality Equations / Medium

You are given an array of strings equations that represent relationships between variables where each string equations[i] is of length 4 and takes one of two different forms: "xi==yi" or "xi!=yi".Here, xi and yi are lowercase letters (not necessarily different) that represent one-letter variable names.

Return true if it is possible to assign integers to variable names so as to satisfy all the given equations, or false otherwise.

<!-- more --> 
 

## Example 1:
> Input: equations = ["a==b","b!=a"]
> Output: false
> Explanation: If we assign say, a = 1 and b = 1, then the first equation is satisfied, but not the second.
> There is no way to assign the variables to satisfy both equations.

## Example 2:
> Input: equations = ["b==a","a==b"]
> Output: true
> Explanation: We could assign a = 1 and b = 1 to satisfy both equations.

## Constraints: 
> - 1 <= equations.length <= 500
> - equations[i].length == 4
> - equations[i][0] is a lowercase letter.
> - equations[i][1] is either '=' or '!'.
> - equations[i][2] is '='.
> - equations[i][3] is a lowercase letter.

# Solution 1: Union-Find
## 思路 

先掃描過 == 的方程式，利用 Union-Find 的方式把集合都建立出來。再掃描 != 的方程式，如果發現有兩者是屬於同一個集合，就代表方程式有問題。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(N)

### LeetCode Result

- Runtime:  ms
- Memory Usage:  MB 
- 

## Code
```cpp
class Solution {
public:
    bool equationsPossible(vector<string>& equations) {
        constexpr int SIZE = 26;
        vector<int> root(SIZE);     
        iota(root.begin(), root.end(), 0);
        
        function<int(int)> find = [&](int x) {
            if (root[x] != x) {
                root[x] = find(root[x]);
            }
            return find(root[x]);
        };
        
        auto unionSet = [&](int x, int y) {
            int rx = find(x), ry = find(y);
            root[rx] = ry;
        };

        for (string& eqn : equations) {
            if (eqn[1] == '=') {
                int x = eqn[0] - 'a'; 
                int y = eqn[3] - 'a';
                unionSet(x, y);
            }
        }

        for (string& eqn : equations) {
            if (eqn[1] == '!') {
                int x = eqn[0] - 'a';
                int y = eqn[3] - 'a';
                if (find(x) == find(y)) {
                    return false;
                }
            }
        }
        return true;
    }
};


```

# Solution 2: DFS
## 思路 

和 Union-Find 的解法很相似。先把 == 的方程式建成圖，再用 DFS 的方式看看有沒有 != 但是屬於同一張圖的，那就代表方程式有問題。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(N)

### LeetCode Result

- Runtime:  ms
- Memory Usage: MB 
- 

## Code
```cpp
class Solution {
public:
    bool equationsPossible(vector<string>& equations) {
        constexpr int SIZE = 26;
        vector<vector<int>> graph(SIZE, vector<int>());
        vector<int> color(SIZE, -1);
        
        for(string& eqn: equations) {
            if(eqn[1] == '=') {
                int x = eqn[0] - 'a';
                int y = eqn[3] - 'a';
                graph[x].emplace_back(y);
                graph[y].emplace_back(x);
            }
        }
        
        function<void(int, int)> dfs = [&](int node, int c) {
            if(color[node] == -1) {
                color[node] = c;
                for(int nei: graph[node]) {
                    dfs(nei, c);
                }
            }
        };
        
        for(int i = 0; i < SIZE; ++i) dfs(i, i);
        
        for(string& eqn: equations) {
            if(eqn[1] == '!') {
                int x = eqn[0] - 'a';
                int y = eqn[3] - 'a';
                if(color[x] == color[y]) return false;
            }
        }
        return true;
    }
};
```