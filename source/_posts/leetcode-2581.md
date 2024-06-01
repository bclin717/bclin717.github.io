---
title: 【LeetCode】2581. Count Number of Possible Root Nodes 解題報告
categories:
  - LeetCode
date: 2023-03-15 13:48:00
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/V1a5WnH.jpg
---
 
# 2581. Count Number of Possible Root Nodes / Hard

Alice has an undirected tree with n nodes labeled from 0 to n - 1. The tree is represented as a 2D integer array edges of length n - 1 where edges[i] = [ai, bi] indicates that there is an edge between nodes ai and bi in the tree.

Alice wants Bob to find the root of the tree. She allows Bob to make several guesses about her tree. In one guess, he does the following:

- Chooses two distinct integers u and v such that there exists an edge [u, v] in the tree.
- He tells Alice that u is the parent of v in the tree.

Bob's guesses are represented by a 2D integer array guesses where guesses[j] = [uj, vj] indicates Bob guessed uj to be the parent of vj.

Alice being lazy, does not reply to each of Bob's guesses, but just says that at least k of his guesses are true.

Given the 2D integer arrays edges, guesses and the integer k, return the number of possible nodes that can be the root of Alice's tree. If there is no such tree, return 0.
<!-- more --> 

## Example 1:
![](https://assets.leetcode.com/uploads/2022/12/19/ex-1.png)
> Input: edges = [[0,1],[1,2],[1,3],[4,2]], guesses = [[1,3],[0,1],[1,0],[2,4]], k = 3
> Output: 3
> Explanation: 
> Root = 0, correct guesses = [1,3], [0,1], [2,4]
> Root = 1, correct guesses = [1,3], [1,0], [2,4]
> Root = 2, correct guesses = [1,3], [1,0], [2,4]
> Root = 3, correct guesses = [1,0], [2,4]
> Root = 4, correct guesses = [1,3], [1,0]
> Considering 0, 1, or 2 as root node leads to 3 correct guesses.

## Example 2:
![](https://assets.leetcode.com/uploads/2022/12/19/ex-2.png)
> Input: edges = [[0,1],[1,2],[2,3],[3,4]], guesses = [[1,0],[3,4],[2,1],[3,2]], k = 1
> Output: 5
> Explanation: 
> Root = 0, correct guesses = [3,4]
> Root = 1, correct guesses = [1,0], [3,4]
> Root = 2, correct guesses = [1,0], [2,1], [3,4]
> Root = 3, correct guesses = [1,0], [2,1], [3,2], [3,4]
> Root = 4, correct guesses = [1,0], [2,1], [3,2]
> Considering any node as root will give at least 1 correct guess. 

## Comstraints
> - edges.length == n - 1
> - 2 <= n <= 10^5
> - 1 <= guesses.length <= 10^5
> - 0 <= ai, bi, uj, vj <= n - 1
> - ai != bi
> - uj != vj
> - edges represents a valid tree.
> - guesses[j] is an edge of the tree.
> - guesses is unique.
> - 0 <= k <= guesses.length

# Solution
## 思路

![](https://assets.leetcode.com/users/images/6e0c52d4-c334-4a3d-85dc-f02162aca306_1677945696.526142.png)


## 效能

### Complexity 
- Time Complexity: O(NlogN), where N is the number of vertex
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 782 ms
- Memory Usage: 241.7 MB 
- https://leetcode.com/problems/count-number-of-possible-root-nodes/submissions/914367974/

## Code
```cpp
class Solution {
public:
    int anss = 0;
    map<pair<int, int>, bool> mp;

    int get_ans(unordered_map<int, list<int>>& graph, int src, vector<bool>& seen) {
        seen[src] = true;
        int ans = 0;
        for(auto next: graph[src]) {
            if(!seen[next]) {
                ans += (mp.count({src, next}) + get_ans(graph, next, seen));
            }
        }
        return ans;
    }

    void dfs(unordered_map<int, list<int>>& graph, vector<bool>& seen, int k, int src, int ans) {
        if(ans >= k) ++anss;
        seen[src] = 1;
        for(auto& next: graph[src]) {
            if(!seen[next]) {
                dfs(graph, seen, k, next, ans - mp.count({src, next}) + mp.count({next, src}));
            }
        }
    }

    int rootCount(vector<vector<int>>& edges, vector<vector<int>>& guesses, int k) {
        unordered_map<int, list<int>> graph;
        for(const auto& edge: edges) {
            graph[edge[0]].push_back(edge[1]);
            graph[edge[1]].push_back(edge[0]);
        }

        for(const auto& g: guesses) {
            mp[{g[0], g[1]}] = 1;
        }
         
        int n = edges.size()+1;
        vector<bool> seen(n, 0);
        int ans = get_ans(graph, 0, seen);
        fill(seen.begin(), seen.end(), 0);
        dfs(graph, seen, k, 0, ans);
        return anss;
    }
};
```
