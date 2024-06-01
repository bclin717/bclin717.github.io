---
title: 【LeetCode】454. 4Sum II 解題報告
categories:
  - LeetCode
date: 2022-05-29 16:27:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/71kSny1.jpg
---
 
# 454. 4Sum II / Medium

Given four integer arrays nums1, nums2, nums3, and nums4 all of length n, return the number of tuples (i, j, k, l) such that:

- 0 <= i, j, k, l < n
- nums1[i] + nums2[j] + nums3[k] + nums4[l] == 0

<!-- more --> 

## Example 1:
> Input: nums1 = [1,2], nums2 = [-2,-1], nums3 = [-1,2], nums4 = [0,2]
> Output: 2
> Explanation:
> The two tuples are:
> 1. (0, 0, 0, 1) -> nums1[0] + nums2[0] + nums3[0] + nums4[1] = 1 + (-2) + (-1) + 2 = 0
> 2. (1, 1, 0, 0) -> nums1[1] + nums2[1] + nums3[0] + nums4[0] = 2 + (-1) + (-1) + 0 = 0

## Example 2:
> Input: nums1 = [0], nums2 = [0], nums3 = [0], nums4 = [0]
> Output: 1

## Comstraints
> - n == nums1.length
> - n == nums2.length
> - n == nums3.length
> - n == nums4.length
> - 1 <= n <= 200
> - -2^28 <= nums1[i], nums2[i], nums3[i], nums4[i] <= 2^28

# Solution: Hash Map / Two Sum
## 思路

4Sum 的本質其實就是 [Two Sum](https://bclin.tw/2021/07/12/leetcode-1/) 的變化型。
這種題目如果用了三層以上甚至四層的迴圈，基本上都會超時，因此我們必須想辦法解少迴圈計算組合的開銷。

思路其實很簡單，將 nums1,nums2 以及 nums3,nums4 先分成兩部分，各自計算出它們之間的組合（每次雙迴圈只要 O(N^2)），
以 Example 1 來說，此時會變成兩個 vector
- v1(nums1 + nums2): [-1, 0, 0, 1]
- v2(nums3 + nums4): [-1. 1, 2, 4]

接著我們將 v1 放進 HashMap，此時 HashMap 會呈現：
- "-1": 1
- "0": 2
- "1": 1

如此一來，我們只要將 v2 的值乘上 -1，去看看這個值有沒有在 HashMap 中，如果有的話，有幾個組合，並將其加總起來就好。

## 效能

### Complexity 
- Time Complexity: O(N^2)
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 650 ms
- Memory Usage: 86.5 MB 
- https://leetcode.com/submissions/detail/709642546/

## Code
```cpp
class Solution {
public:
    int fourSumCount(vector<int>& nums1, vector<int>& nums2, vector<int>& nums3, vector<int>& nums4) {
        unordered_map<int, int> hmap1;
        int count = 0;
        
        vector<int> v1;
        for(auto& num1: nums1) {
            for(auto& num2: nums2) {
                v1.emplace_back(num1 + num2);
            }
        }
        
        vector<int> v2;
        for(auto& num3: nums3) {
            for(auto& num4: nums4) {
                v2.emplace_back(num3 + num4);
            }
        }
        
        for(auto& num: v1) {
            ++hmap1[num];
        }
        
        for(auto& num: v2) {
            if(hmap1.find(-num) != hmap1.end()) count += hmap1[-num];
        }
        return count;
        
        
    }
};
```

以下是經過優化後的程式碼

## 效能

### Complexity 
- Time Complexity: O(N^2)
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 217 ms
- Memory Usage: 24.3 MB 
- https://leetcode.com/submissions/detail/709645335/

## Code
```cpp
class Solution {
public:
    int fourSumCount(vector<int>& nums1, vector<int>& nums2, vector<int>& nums3, vector<int>& nums4) {
        unordered_map<int, int> hmap;
        int count = 0;

        for(const auto& num1: nums1) {
            for(const auto& num2: nums2) {
                ++hmap[num1+num2];
            }
        }
        
        for(const auto& num3: nums3) {
            for(const auto& num4: nums4) {
                if(const auto it = hmap.find(-(num3+num4)); it != hmap.end()) {
                    count += it->second;
                }
            }
        }
        return count;
    }
};
```