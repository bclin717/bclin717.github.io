---
title: 【LeetCode】815. Bus Routes 解題報告
categories:
  - LeetCode
date: 2022-08-24 12:45:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/Rm3eRN6.png
---
 
# 815. Bus Routes / Hard

You are given an array routes representing bus routes where routes[i] is a bus route that the ith bus repeats forever.

For example, if routes[0] = [1, 5, 7], this means that the 0th bus travels in the sequence 1 -> 5 -> 7 -> 1 -> 5 -> 7 -> 1 -> ... forever.
You will start at the bus stop source (You are not on any bus initially), and you want to go to the bus stop target. You can travel between bus stops by buses only.

Return the least number of buses you must take to travel from source to target. Return -1 if it is not possible.

<!-- more --> 
 

## Example 1:
> Input: routes = [[1,2,7],[3,6,7]], source = 1, target = 6
> Output: 2
> Explanation: The best strategy is take the first bus to the bus stop 7, then take the second bus to the bus stop 6.

## Example 2:
> Input: routes = [[7,12],[4,5,15],[6],[15,19],[9,12,13]], source = 15, target = 12
> Output: -1

## Constraints: 
> - 1 <= routes.length <= 500.
> - 1 <= routes[i].length <= 10^5
> - All the values of routes[i] are **unique**.
> - sum(routes[i].length) <= 10^5
> - 0 <= routes[i][j] < 10^6
> - 0 <= source, target < 10^6

# Solution: BFS
## 思路 

routes 這個陣列存的是公車的路線，不能直接拿來建圖。
可以理解為，routes[n] 是 n 號公車的路線（所有的站牌）。

首先用一個 HashMap 來建立 站牌對應的公車，也就是說，當我到了某個站牌，能搭到哪些公車？

接著將起始站與轉車次數作為一個 pair 放進 q 做 BFS。
每次我們都可以透過剛剛建的 map 拿到可以轉搭哪些公車，又可以從這些公車拿到有哪些下一站，搭過的路線我們就 clear 掉。
因此不需要紀錄走過的站牌避免重複。 

如果搭到的是目的地，那就將轉車次數 return。


## 效能

### Complexity 
- Time Complexity: O(MN), where M is number of buses and N is number of routes.
- Space Complexity: O(MN)

### LeetCode Result

- Runtime: 262 ms
- Memory Usage: 53.2 MB 
- https://leetcode.com/submissions/detail/781828611/

## Code
```cpp
class Solution {
public:
    int numBusesToDestination(vector<vector<int>>& routes, int S, int T) {
        unordered_map<int, vector<int>> route_to_bus;
        for(int i = 0; i < routes.size(); ++i) {
            for(int stop: routes[i]) {
                route_to_bus[stop].emplace_back(i);
            }
        }
        queue<pair<int, int>> q;
        q.push({S, 0});
        while(!q.empty()) {
            int stop = q.front().first;
            int times = q.front().second;
            q.pop();
            if(stop == T) return times;
            
            for(int nextBus: route_to_bus[stop]) {
                for(int nextStop: routes[nextBus]) {
                    q.push({nextStop, times+1});
                }
                routes[nextBus].clear();
            }
        }
        return -1;
    }
};
```