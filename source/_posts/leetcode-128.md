---
title: 【LeetCode】128. Longest Consecutive Sequence 解題報告
categories:
  - LeetCode
date: 2021-09-05 23:57:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/nfFnZfa.jpg
---
 
# 128. Longest Consecutive Sequence / Medium
Given an unsorted array of integers nums, return the length of the longest consecutive elements sequence.

You must write an algorithm that runs in O(n) time.

<!-- more --> 
 

## Example 1:
> Input: nums = [100,4,200,1,3,2]
> Output: 4
> Explanation: The longest consecutive elements sequence is [1, 2, 3, 4]. Therefore its length is 4.

## Example 2:
> Input: nums = [0,3,7,2,5,8,4,6,0,1]
> Output: 9

## Constraints: 
> - 0 <= nums.length <= 10^5
> - -10^9 <= nums[i] <= 10^9

# Solution
## 思路 

先把所有的元素都放進 unordered_set (Hash Set)，每一個元素進來先檢查是不是存在，存在的話把他從 set 移除，避免之後重複判斷。
接著把數字往前、往後增加，檢查 hset 是否有這些數字，最後計算出長度，和 max 相比，更新 max 的數值。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 60 ms
- Memory Usage: 29 MB 
- https://leetcode.com/submissions/detail/544481421/

## Code
```cpp
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        if(nums.empty()) return 0;
        unordered_set<int> hset(nums.begin(), nums.end());
        
        int maxLCS = INT_MIN;
        for(const auto& num: nums) {
            if(hset.count(num) != 0) {
                hset.erase(num);
                int next = num+1, prev = num-1;
                while(hset.count(next) != 0) hset.erase(next++);
                while(hset.count(prev) != 0) hset.erase(prev--);
                maxLCS = max(next-prev-1, maxLCS);
            }
        }
        return maxLCS;
    }
};
```