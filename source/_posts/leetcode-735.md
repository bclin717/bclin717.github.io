---
title: 【LeetCode】735. Asteroid Collision 解題報告
categories:
  - LeetCode
date: 2021-08-01 13:01:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/Pu5JGvi.jpg
---
 
# 735. Asteroid Collision / Medium
We are given an array `asteroids` of integers representing asteroids in a row.

For each asteroid, the absolute value represents its size, and the sign represents its direction (positive meaning right, negative meaning left). Each asteroid moves at the same speed.

Find out the state of the asteroids after all collisions. If two asteroids meet, the smaller one will explode. If both are the same size, both will explode. Two asteroids moving in the same direction will never meet.

<!-- more --> 
 

## Example 1:
> Input: asteroids = [5,10,-5]
> Output: [5,10]
> Explanation: The 10 and -5 collide resulting in 10. The 5 and 10 never collide.

## Example 2:
> Input: asteroids = [8,-8]
> Output: []
> Explanation: The 8 and -8 collide exploding each other.

## Example 3:
> Input: asteroids = [10,2,-5]
> Output: [10]
> Explanation: The 2 and -5 collide resulting in -5. The 10 and -5 collide resulting in 10.

## Constraints: 
> - 2 <= asteroids.length <= 10^4
> - -1000 <= asteroids[i] <= 1000
> - asteroids[i] != 0

# Solution
## 思路 

這題原來是用 stack 解，但比較複雜，改用陣列解簡單很多。
如果目前解是空的，解最後一個是往左飛(小於零)，或是新進來的往右飛(大於零)，就直接放進解。
因為最後解的狀態一定是左邊的往左飛，右邊的往右飛。
如果解的最後小於等於目前小行星的絕對值，代表會相撞。當解的最後(向右飛)小於等於目前往左飛來小行星的絕對值，
解的最後會不見(pop_back)，而往左的小行星比較大的話，就把 index i-1，讓指標繼續指在這顆小行星。

## 效能

### Complexity 
- Time Complexity: O(N) 
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 20 ms
- Memory Usage: 17.5 MB 
- https://leetcode.com/submissions/detail/761303437/

## Code
```cpp
class Solution {
public:
    vector<int> asteroidCollision(vector<int>& a) {
        vector<int> res;
        for(int i = 0; i < a.size(); ++i) {
            if(res.empty() || a[i] > 0 || res.back() < 0) {
                res.emplace_back(a[i]);
            } else if(res.back() <= -a[i]) {
                if(res.back() < -a[i]) --i;
                res.pop_back();
            }
        }
        return res;
    }
};
```