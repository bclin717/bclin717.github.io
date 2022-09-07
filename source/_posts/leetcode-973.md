---
title: 【LeetCode】973. K Closest Points to Origin 解題報告
categories:
  - LeetCode
date: 2022-09-07 10:51:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/opzBDTj.jpg
---
 
# 973. K Closest Points to Origin / Medium

Given an array of points where points[i] = [xi, yi] represents a point on the X-Y plane and an integer k, return the k closest points to the origin (0, 0).

The distance between two points on the X-Y plane is the Euclidean distance (i.e., √(x1 - x2)^2 + (y1 - y2)^2).

You may return the answer in any order. The answer is guaranteed to be unique (except for the order that it is in).


<!-- more --> 
 

## Example 1:
![](https://assets.leetcode.com/uploads/2021/03/03/closestplane1.jpg)
> Input: points = [[1,3],[-2,2]], k = 1
> Output: [[-2,2]]
> Explanation:
> The distance between (1, 3) and the origin is sqrt(10).
> The distance between (-2, 2) and the origin is sqrt(8).
> Since sqrt(8) < sqrt(10), (-2, 2) is closer to the origin.
> We only want the closest k = 1 points from the origin, so the answer is just [[-2,2]].

## Example 2:
> Input: points = [[3,3],[5,-1],[-2,4]], k = 2
> Output: [[3,3],[-2,4]]
> Explanation: The answer [[-2,4],[3,3]] would also be accepted.

## Constraints: 
> - 1 <= k <= points.length <= 10^4
> - -10^4 < xi, yi < 10^4

# Solution 1: Heap
## 思路 

看到要找前 K 個，直覺用 Heap (Priority Queue)
將 點與原點 的距離 與 index 放進 priority queue 中，
按照距離大小排列，越遠的點會再越前面，
如果 PQ 中的數量超過 K 個，就把最大的 pop 掉。
最後再將 PQ 中紀錄的前 K 個值，放進二維陣列回傳。

## 效能

### Complexity 
- Time Complexity: O(Nlog(k)), where N is the number of points
- Space Complexity: O(k)

### LeetCode Result

- Runtime: 443 ms
- Memory Usage: 62.3 MB 
- https://leetcode.com/submissions/detail/753011006/

## Code
```cpp
class Solution {
public:
    vector<vector<int>> kClosest(vector<vector<int>>& points, int k) {
        auto cmp = [](auto& a, auto& b) {
            return a[0]*a[0] + a[1]*a[1] < b[0]*b[0] + b[1]*b[1];
        };
        
        priority_queue<vector<int>, vector<vector<int>>, decltype(cmp)> pq(cmp);
        for(auto& point: points) {
            pq.push(point);
            if(pq.size() > k) pq.pop();
        }
        
        vector<vector<int>> res;
        while(!pq.empty()) {
            res.push_back(pq.top());
            pq.pop();
        }
    
        return res;
    }
};
```

# Solution 2: QuickSelect
## 思路 

我們可以用 QuickSelect 做優化，
利用 QuickSelect 可以確定第 K 個值會是第 K 大，同時保證 K 前面的值都是小於 K，後面的值都是大於 K。

## 效能

### Complexity 
- Time Complexity: O(N), where N is the number of points
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 175 ms
- Memory Usage: 57.1 MB 
- https://leetcode.com/submissions/detail/793509910/

## Code
```cpp
class Solution {
public:
    vector<vector<int>> kClosest(vector<vector<int>>& points, int k) {
        auto cmp = [](vector<int>& a, vector<int>& b) {
            return a[0]*a[0] + a[1]*a[1] < b[0]*b[0] + b[1]*b[1];
        };
        
        nth_element(points.begin(), points.begin()+k, points.end(), cmp);
        vector<vector<int>> res(points.begin(), points.begin()+k);
        return res;
    }
};
```