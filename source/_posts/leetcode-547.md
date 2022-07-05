---
title: 【LeetCode】547. Number of Provinces 解題報告
categories:
  - LeetCode
date: 2022-07-06 05:46:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/qlP4BQn.jpg
---
 
# 547. Number of Provinces / Medium

There are n cities. Some of them are connected, while some are not. If city a is connected directly with city b, and city b is connected directly with city c, then city a is connected indirectly with city c.

A province is a group of directly or indirectly connected cities and no other cities outside of the group.

You are given an `n x n` matrix isConnected where `isConnected[i][j]` = 1 if the `ith` city and the `jth` city are directly connected, and `isConnected[i][j]` = 0 otherwise.

Return the total number of provinces.

<!-- more --> 

## Example 1:
![](https://assets.leetcode.com/uploads/2020/12/24/graph1.jpg)
> Input: isConnected = [[1,1,0],[1,1,0],[0,0,1]]
> Output: 2

## Example 2:
![](https://assets.leetcode.com/uploads/2020/12/24/graph2.jpg)
> Input: isConnected = [[1,0,0],[0,1,0],[0,0,1]]
> Output: 3

## Comstraints
> - 1 <= n <= 200
> - n == isConnected.length
> - n == isConnected[i].length
> - isConnected[i][j] is 1 or 0.
> - isConnected[i][i] == 1
> - isConnected[i][j] == isConnected[j][i]

# Solution 1: DFS
## 思路

這種分群可以用 DFS 或 Union-Find (Disjoint-Set) 來解，先說 DFS。
按順序 dfs 每一個點，用 visited 陣列紀錄走過的位置，走過後就不再走。每次找到新的起點就將答案加一。

## 效能

### Complexity 
- Time Complexity: O(N^2), where N is the number of province
- Space Complexity: O(N), where N is the number of province

### LeetCode Result

- Runtime: 37 ms
- Memory Usage: 13.7 MB 
- https://leetcode.com/submissions/detail/721262528/

## Code
```cpp
class Solution {
public:
    int findCircleNum(vector<vector<int>>& isConnected) {
        int n = isConnected.size();
        vector<bool> visited(n, false);
        
        int ans = 0;
        for(int i = 0; i < n; ++i) {
            if(!visited[i]) {
                ++ans;
                dfs(isConnected, i, visited);
            }
        }
        return ans;
    }
    
    void dfs(vector<vector<int>>& isConnected, int point, vector<bool>& visited) {
        if(visited[point]) return;
        visited[point] = true;
        for(int i = 0; i < isConnected.size(); ++i) {
            if(isConnected[point][i]) {
                dfs(isConnected, i, visited);
            }
        }
    }
};
```

# Solution 2: Union-Find (Disjoint Set)
## 思路


## 效能

### Complexity 
- Time Complexity: O(N^3), where N is the number of province
- Space Complexity: O(N), where N is the number of province

### LeetCode Result

- Runtime: 56 ms
- Memory Usage: 13.7 MB 
- https://leetcode.com/submissions/detail/739491832/

## Code
```cpp
class Solution {
public:
    int findCircleNum(vector<vector<int>>& isConnected) {
        vector<int> parent(isConnected.size(), -1);
    
        for(int i = 0; i < isConnected.size(); ++i) {
            for(int j = 0; j < isConnected.size(); ++j) {
                if(isConnected[i][j] && i != j) 
                    unionf(parent, i, j);
            }
        }
        
        int count = 0;
        for(int i = 0; i < parent.size(); ++i) {
            if(parent[i] == -1) ++count;
        }
        return count;
    }
    
    void unionf(vector<int>& parent, int x, int y) {
        int xset = find(parent, x);
        int yset = find(parent, y);
        if(xset != yset) {
            parent[xset] = yset;
        }
    }
    
    int find(vector<int>& parent, int i) {
        if(parent[i] == -1) return i;
        return find(parent, parent[i]);
    }
};
```


# Solution 3: Union-Find (Disjoint Set) with Path Compression / Union by Rank
## 思路

Union-Find 可以透過兩個技巧來加速，透過在 Find 時將 parent[i] 指向回傳的 root，來減少遞迴時的時空間損失。
而 Union by size 則可以減少 Path Compression 的次數。


## 效能

### Complexity 
- Time Complexity: O(N^2), where N is the number of province
- Space Complexity: O(N), where N is the number of province

### LeetCode Result

- Runtime: 25 ms
- Memory Usage: 13.7 MB 
- https://leetcode.com/submissions/detail/739497218/

## Code
```cpp
class Solution {
public:
    int findCircleNum(vector<vector<int>>& isConnected) {
        int sz = isConnected.size();
        count = sz;
        vector<int> parent(sz, -1), rank(sz, 1);
        
        for(int i = 0; i < sz; ++i) {
            for(int j = 0; j < sz; ++j) {
                if(isConnected[i][j] && i != j) 
                    unionf(rank, parent, i, j);
            }
        }
        return count;
    }
    
    void unionf(vector<int>& rank, vector<int>& parent, int x, int y) {
        int xset = find(parent, x);
        int yset = find(parent, y);
        if(xset != yset) {
            if(rank[xset] > rank[yset]) {
                parent[yset] = xset;
                rank[xset] += rank[yset];
            }
            else if(rank[xset] <= rank[yset]) {
                parent[xset] = yset;
                rank[yset] += rank[xset];
            }
            --count;
        }
    }
    
    int find(vector<int>& parent, int i) {
        if(parent[i] == -1) return i;
        return parent[i] = find(parent, parent[i]);
    }
private:
    int count;
};
```