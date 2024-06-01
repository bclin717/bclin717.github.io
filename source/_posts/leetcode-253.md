---
title: 【LeetCode】253. Meeting Rooms II 解題報告
categories:
  - LeetCode
date: 2022-06-23 9:31:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/WyQdpC8.jpg
---
 
# 253. Meeting Rooms II / Medium

Given an array of meeting time intervals `intervals` where `intervals[i] = [starti, endi]`, return the minimum number of conference rooms required.

<!-- more --> 

## Example 1:
> Input: intervals = [[0,30],[5,10],[15,20]]
> Output: 2

## Example 2:
> Input: intervals = [[7,10],[2,4]]
> Output: 1


## Comstraints
> - `1 <= intervals.length <= 10^4`
> - `0 <= starti < endi <= 10^6`

# Solution: Sort + MinHeap (Priority Queue)
## 思路

首先，我們用 sort 將 intervals 按照 start time 排序。排序後，只要迴圈順序取出，就會是開始使用會議室的順序。
接下來我們宣告一個 priority_queue （min heap），用來存目前房間的使用狀況。
順序取出 interval，開始使用房間的時候，就將房間的 end time 存入 min heap，這時最先結束的會議室最排在最前面。
每次存入，都檢查最先結束的會議室的結束時間，是否小於現在正要使用的開始時間，如果是，就將這個會議室 pop 出去，再 push 新的結束時間進去（類似於接管會議室）。
如果不是，代表還沒有人結束，只好再開一間新的會議室。
最後，使用過的房間數量就是 min heap 的 size。

## 效能

### Complexity 
- Time Complexity: O(NlogN), where N is the length of interval.
- Space Complexity: O(N), where N is the length of interval.

### LeetCode Result

- Runtime: 19 ms
- Memory Usage: 11.9 MB 
- https://leetcode.com/submissions/detail/728455674/

## Code
```cpp
class Solution {
public:
    int minMeetingRooms(vector<vector<int>>& intervals) {
        sort(intervals.begin(), intervals.end());
        priority_queue<int, vector<int>, greater<int>> free_room;
        for(const auto& interval: intervals) {
            if(!free_room.empty() && free_room.top() <= interval[0]) 
                free_room.pop();
            free_room.push(interval[1]);
        }
        return free_room.size();
    }
};
```