---
title: 【LeetCode】56. Merge Intervals 解題報告
categories:
  - LeetCode
date: 2021-08-09 00:05:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/Dt1SZ7x.jpg
---
 
# 56. Merge Intervals / Medium

Given an array of intervals where intervals[i] = [starti, endi], merge all overlapping intervals, and return an array of the non-overlapping intervals that cover all the intervals in the input.
 

<!-- more --> 
## Example 1:
> Input: intervals = [[1,3],[2,6],[8,10],[15,18]]
> Output: [[1,6],[8,10],[15,18]]
> Explanation: Since intervals [1,3] and [2,6] overlaps, merge them into [1,6].

## Example 2:
> Input: intervals = [[1,4],[4,5]]
> Output: [[1,5]]
> Explanation: Intervals [1,4] and [4,5] are considered overlapping.

## Constraints:
> - 1 <= intervals.length <= 104
> - intervals[i].length == 2
> - 0 <= starti <= endi <= 104
 


# Solution: Sorting
## 思路

想要判斷兩個區間 [a,b] 與 [c,d] 是重疊，有兩個條件
1. a <= c
2. c-b <= 0


第一個條件可以藉由排序達成，所以我們先把 intervals 排序，這時裡面的 vector 會按照第一個元素的大小排列。
接下來只要判斷第二個條件就可以了，如果 c 比 b 大，那就不是重疊了，可以直接把目前的 vector push進最後解答的 vector。如果重疊，我們就已經確定左邊界了（因為排序過，a 一定是最小的），至於右邊界，則是 max(b,d)，有可能出現 [c,d] 是 [a,b] 的子區間這種狀況。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 16 ms
- Memory Usage: 14.1 MB 
- https://leetcode.com/submissions/detail/535062959/

## Code
```cpp
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        sort(intervals.begin(), intervals.end());
        vector<vector<int>> sol;
        for(const auto& interval: intervals) {
            if(sol.empty() || sol.back()[1] < interval[0]) {
                sol.emplace_back(interval);
            } else {
                sol.back()[1] = max(sol.back()[1], interval[1]);
            }
        }
        return sol;
    }
};
```