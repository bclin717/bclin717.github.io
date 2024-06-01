---
title: 【LeetCode】658. Find K Closest Elements 解題報告
categories:
  - LeetCode
date: 2022-08-01 14:09:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/Qyezlvj.jpg
---
 
# 658. Find K Closest Elements / Medium
Given a sorted integer array arr, two integers k and x, return the k closest integers to x in the array. The result should also be sorted in ascending order.

An integer a is closer to x than an integer b if:
- `|a - x| < |b - x|`, or
- `|a - x| == |b - x|` and `a < b`
 
<!-- more --> 

## Example 1:
> Input: arr = [1,2,3,4,5], k = 4, x = 3
> Output: [1,2,3,4]

## Example 2:
> Input: arr = [1,2,3,4,5], k = 4, x = -1
> Output: [1,2,3,4]

## Constraints: 
> - 1 <= k <= arr.length
> - 1 <= arr.length <= 10^4
> - arr is sorted in ascending order.
> - -10^4 <= arr[i], x <= 10^4


# Solution 1: Priority Queue
## 思路 

我們可以用 Priority Queue 來存點與距離，並自訂 comparator 讓他按照距離排序。
如果距離一樣，就按照點的大小來排序。
如果 pq 的 size 大於 K 就把最小的 pop 出去。

最後記得重新 sort 答案陣列符合題目需求。

因此整個作法的時間複雜度為 O(Nlog(K) + Klog(K))。


## 效能

### Complexity 
- Time Complexity: O(Nlog(K) + Klog(K))
- Space Complexity: O(K), where the priority queue uses O(K).

### LeetCode Result

- Runtime: 123 ms
- Memory Usage: 34 MB 
- https://leetcode.com/submissions/detail/759791599/

## Code
```cpp
class Solution {
public:
    vector<int> findClosestElements(vector<int>& arr, int k, int x) {
        auto cmp = [](auto& a, auto& b) {
            if(a.second == b.second) {
                return a.first < b.first;
            }
            return a.second < b.second;
        };
        
        priority_queue<pair<int, int>, vector<pair<int, int>>, decltype(cmp)> pq(cmp);
        
        for(auto& num: arr) {
            int dis = abs(num-x);
            pq.push({num, dis});
            if(pq.size() > k) pq.pop();
        }
        
        vector<int> res;
        while(!pq.empty()) {
            res.emplace_back(pq.top().first);
            pq.pop();
        }
        sort(res.begin(), res.end());
        return res;
    }
};
```

# Solution 2: Binary Search
## 思路 

這題可以用 Binary Search 解，但有點 tricky。
一開始右邊界就少了 k，為了最後符合 k 個需求。
如果 arr[mid] 比 arr[mid+k] 近，我們就把 right 往左移動。
最後 left ~ left + k 即是我們要的答案。

至於這裡為什麼要用 x - arr[mid] <= arr[mid+k] - x 而不是絕對值，
是因為如果用絕對值來比較，如果大家值都相同的狀態下，必須額外再去判斷其他狀況。
例如遇到這個測資，用絕對值比較會錯。
> A = [1,1,2,2,2,2,2,3,3], x = 3, k = 3

## 效能

### Complexity 
- Time Complexity: O(log(N-K) + K), it takes O(log(N−K)) time from the binary search, it still costs us O(K) to build the final output.
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 46 ms
- Memory Usage: 31.6 MB 
- https://leetcode.com/submissions/detail/762080699/

## Code
```cpp
class Solution {
public:
    vector<int> findClosestElements(vector<int>& arr, int k, int x) {
        int left = 0, right = arr.size()-k;
        
        while(left < right) {
            int mid = left+(right-left)/2;
            if(x - arr[mid] <= arr[mid+k] - x) {
                right = mid;
            } else {
                left = mid + 1; 
            }
        }
        
        vector<int> res;
        for(int i = left; i < left + k; ++i) {
            res.emplace_back(arr[i]);
        }
        return res;
    }
};
```