---
title: 【LeetCode】350. Intersection of Two Arrays II 解題報告
categories:
  - LeetCode
date: 2021-08-22 00:36:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/15pv1Nk.jpg
---
 
# 350. Intersection of Two Arrays II / Easy

Given two integer arrays nums1 and nums2, return an array of their intersection. Each element in the result must appear as many times as it shows in both arrays and you may return the result in any order.

<!-- more --> 


## Example 1:
> Input: nums1 = [1,2,2,1], nums2 = [2,2]
> Output: [2,2]

## Example 2:
> Input: nums1 = [4,9,5], nums2 = [9,4,9,8,4]
> Output: [4,9]
> Explanation: [9,4] is also accepted.

## Constraints: 
> - 1 <= nums1.length, nums2.length <= 1000
> - 0 <= nums1[i], nums2[i] <= 1000

# Solution
## 思路

要找的是交集，最簡單的方式便是兩個陣列一個一個比對，找到的話就把他從陣列中移除，並且把交集的數放到最後答案的 vector 中。

因為從 vector 移除（erase）的代價比較高，加上題目保證 vector 內都是大於零的數，於是找到的話就設成 -1 就好，不用真的把它從 vector 清掉。

## 效能

### Complexity 
- Time Complexity: O(N^2)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 0 ms
- Memory Usage: 10 MB 
- https://leetcode.com/submissions/detail/541978447/

## Code
```cpp
class Solution {
public:
    vector<int> intersect(vector<int>& nums1, vector<int>& nums2) {
        vector<int> sol;
        for(const auto& num: nums1) {
            if(auto&& it = find(nums2.begin(), nums2.end(), num); it != nums2.end()) {
                *it = -1;
                sol.emplace_back(num);
            }
        }
        return sol;
    }
};
```