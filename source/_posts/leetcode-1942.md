---
title: 【LeetCode】1942. The Number of the Smallest Unoccupied Chair 解題報告
categories:
  - LeetCode
date: 2022-02-01 18:00:00
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/7duSdXc.jpg
---
 
# 1942. The Number of the Smallest Unoccupied Chair / Medium

There is a party where n friends numbered from 0 to n - 1 are attending. There is an infinite number of chairs in this party that are numbered from 0 to infinity. When a friend arrives at the party, they sit on the unoccupied chair with the smallest number.

For example, if chairs 0, 1, and 5 are occupied when a friend comes, they will sit on chair number 2.
When a friend leaves the party, their chair becomes unoccupied at the moment they leave. If another friend arrives at that same moment, they can sit in that chair.

You are given a 0-indexed 2D integer array times where times[i] = [arrivali, leavingi], indicating the arrival and leaving times of the ith friend respectively, and an integer targetFriend. All arrival times are distinct.

Return the chair number that the friend numbered targetFriend will sit on.

<!-- more --> 

## Example 1:
> Input: times = [[1,4],[2,3],[4,6]], targetFriend = 1
> Output: 1
> Explanation: 
> - Friend 0 arrives at time 1 and sits on chair 0.
> - Friend 1 arrives at time 2 and sits on chair 1.
> - Friend 1 leaves at time 3 and chair 1 becomes empty.
> - Friend 0 leaves at time 4 and chair 0 becomes empty.
> - Friend 2 arrives at time 4 and sits on chair 0.
> Since friend 1 sat on chair 1, we return 1.

## Example 2:
> Input: times = [[3,10],[1,5],[2,6]], targetFriend = 0
> Output: 2
> Explanation: 
> - Friend 1 arrives at time 1 and sits on chair 0.
> - Friend 2 arrives at time 2 and sits on chair 1.
> - Friend 0 arrives at time 3 and sits on chair 2.
> - Friend 1 leaves at time 5 and chair 0 becomes empty.
> - Friend 2 leaves at time 6 and chair 1 becomes empty.
> - Friend 0 leaves at time 10 and chair 2 becomes empty.
> Since friend 0 sat on chair 2, we return 2.

## Comstraints
> - n == times.length
> - 2 <= n <= 104
> - times[i].length == 2
> - 1 <= arrivali < leavingi <= 105
> - 0 <= targetFriend <= n - 1
> - Each arrivali time is distinct.

# Solution 1: Priority Queue
## 思路

一開始，我們將 times 依照抵達時間排序。

接著，宣告兩個資料結構來儲存目前佔位的情形（reserved）以及空的座位（available），reserved 可以用 priority queue 來實現，available 由於只儲存座位的號碼，使用 priority queue 或是 set 都可以（Heap v.s. BinaryTree）。

reserved 的 pair 中，first 存離開的時間，second 則是占據的椅子編號。

接下來，依據每個人的抵達時間，一個一個來看椅子怎麼排。
如果有人抵達了（!reserved.empty()）而且他的離開時間（reserved.top().first）先於目前人的抵達時間，代表這個人該走了。
把他從 reserved 中移開，並且把他的椅子編號放到 available 中。

如果這個抵達的朋友是題目要問的人，那就直接跳出迴圈，並回傳目前 available 的椅子中最小的編號（available.top()），或是給他一把新椅子。

如果 available 的椅子現在是空的，就要拉一把新椅子給這個人。
否則我們就從 available 中拿走最小的椅子（available.top()）給這個朋友。（reserved.push({time[1], available.top()});）

這題可以只用一個 priority queue，但我覺得有點 tricky。

## 效能

### Complexity 
- Time Complexity: O(NlogN), which N is the size of times.
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 250 ms
- Memory Usage: 51.1 MB 
- https://leetcode.com/submissions/detail/632106144/

## Code
```cpp
class Solution {
public:
    int smallestChair(vector<vector<int>>& times, int targetFriend) {
        int targetTime = times[targetFriend][0];
        sort(times.begin(), times.end());
        priority_queue<int, vector<int>, greater<int>> available;
        priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> reserved;
        
        for(auto& time: times) {
            while(!reserved.empty() && reserved.top().first <= time[0]) {
                available.push(reserved.top().second);
                reserved.pop();
            }
            
            if(time[0] == targetTime) break;
            
            if(available.empty()) {
                reserved.push({time[1], reserved.size()});
            } else {
                reserved.push({time[1], available.top()});
                available.pop();
            }
        }
        
        return available.empty() ? reserved.size() : available.top();
    }
};
```

```cpp
class Solution {
public:
    int smallestChair(vector<vector<int>>& times, int targetFriend) {
        int targetTime = times[targetFriend][0];
        sort(times.begin(), times.end());
        priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> reserved;
        
        for(const auto& time: times) {
            while(!reserved.empty() && reserved.top().first < time[0]) {
                reserved.push({time[0], reserved.top().second});
                reserved.pop();
            }
            
            if(time[0] == targetTime) break;
            
            if(reserved.empty() || reserved.top().first > time[0]) {
                reserved.push({time[1], reserved.size()});
            } else {
                reserved.push({time[1], reserved.top().second});
                reserved.pop();
            }
        }
        
        return !reserved.empty() && reserved.top().first <= targetTime ? reserved.top().second : reserved.size();
    }
};
```

# Solution 2: Multimap
## 思路

一樣的概念，不過將 priority_queue 換成 multimap，如此一來便不需要使用 pair。


## 效能

### Complexity 
- Time Complexity: O(NlogN), which N is the size of times.
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 208 ms
- Memory Usage: 57.1 MB 
- https://leetcode.com/submissions/detail/632172693/

## Code
```cpp
class Solution {
public:
   int smallestChair(vector<vector<int>>& times, int targetFriend) {
        int targetTime = times[targetFriend][0];
        sort(times.begin(), times.end());
        multimap<int, int> reserved;
        priority_queue<int, vector<int>, greater<int>> available;

        for (const auto &time: times) {
            while (!reserved.empty() && begin(reserved)->first <= time[0]) {
                available.push(begin(reserved)->second);
                reserved.erase(begin(reserved));
            }
            if (time[0] == targetTime)
                break;
            if (available.empty())
                reserved.insert({time[1], reserved.size()});
            else {
                reserved.insert({time[1], available.top()});
                available.pop();
            }
        }
        return available.empty() ? reserved.size() : available.top();
    }
};
```