---
title: 【LeetCode】239. Sliding Window Maximum 解題報告
categories:
  - LeetCode
date: 2022-01-09 20:53:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/3nOTgfO.jpg
---
 
# 239. Sliding Window Maximum / Hard

You are given an array of integers nums, there is a sliding window of size k which is moving from the very left of the array to the very right. You can only see the k numbers in the window. Each time the sliding window moves right by one position.

Return the max sliding window.

<!-- more --> 

## Example 1:
![](https://assets.leetcode.com/uploads/2020/10/03/merge_ex1.jpg)
> Input: nums = [1,3,-1,-3,5,3,6,7], k = 3
> Output: [3,3,5,5,6,7]
> Explanation: 
> Window position                Max
> ---------------               -----
> [1  3  -1] -3  5  3  6  7       3
> 1 [3  -1  -3] 5  3  6  7        3
> 1  3 [-1  -3  5] 3  6  7        5
> 1  3  -1 [-3  5  3] 6  7        5
> 1  3  -1  -3 [5  3  6] 7        6
> 1  3  -1  -3  5 [3  6  7]       7

## Example 2:
> Input: nums = [1], k = 1
> Output: [1]


## Comstraints
> - 1 <= nums.length <= 10^5
> - -10^4 <= nums[i] <= 10^4
> - 1 <= k <= nums.length

# Solution: Deque
## 思路

利用 Deque 來實現移動 sliding window 儲存數字的 index。
Deque 中的 front 必須是目前這個 window 中最大數字的 index，每次我們都要檢查新的數字是否大於 dq 最後的數字，也就是有機會成為 window 中最大數字的候選中，最小的那一個。如果新的數字比較大，就 pop back。動作持續到整個 Deque 呈現遞減數列為止。

最差狀況下，每一個數字都會被 deque push / pop 各一次，總共操作 2N 次，時間複雜度為 O(N)；Deque 最多存 N 個，空間複雜度 O(N)。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 224 ms
- Memory Usage: 131.7 MB 
- https://leetcode.com/submissions/detail/616147465/

## Code
```cpp
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        deque<int> dq;
        vector<int> ans;
        
        int n = nums.size();
        for(int i = 0; i < n; ++i) {
            int front = i - k + 1;
            while(!dq.empty() && dq.back() < nums[i]) dq.pop_back();
            dq.push_back(nums[i]);
            if(front >= 0) {
                ans.emplace_back(dq.front());
                if(nums[front] == dq.front()) dq.pop_front();
            } 
        }
        return ans;
    }
};
```