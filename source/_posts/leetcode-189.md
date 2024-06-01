---
title: 【LeetCode】189. Rotate Array 解題報告
categories:
  - LeetCode
date: 2021-08-21 14:56:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/qT8nU4S.jpg
---
 
# 189. Rotate Array / Medium

Given an array, rotate the array to the right by k steps, where k is non-negative.

<!-- more --> 


## Example 1:
> Input: nums = [1,2,3,4,5,6,7], k = 3
> Output: [5,6,7,1,2,3,4]
> Explanation:
> rotate 1 steps to the right: [7,1,2,3,4,5,6]
> rotate 2 steps to the right: [6,7,1,2,3,4,5]
> rotate 3 steps to the right: [5,6,7,1,2,3,4]

## Example 2:
> Input: nums = [-1,-100,3,99], k = 2
> Output: [3,99,-1,-100]
> Explanation: 
> rotate 1 steps to the right: [99,-1,-100,3]
> rotate 2 steps to the right: [3,99,-1,-100]


## Constraints: 
> - 1 <= nums.length <= 10^5
> - -2^31 <= nums[i] <= 2^31 - 1
> - 0 <= k <= 10^5


# Solution 1: Insert & Erase 
## 思路

既然題目要求要 O(1) 的空間複雜度，也就是要 In-Place，那就只好使用 vector 操作了。

要進行旋轉，其實就是把後面 k 個搬到前面，但 vector 在最前面 insert 效能花費比較大（雖說怎麼也避不開就是了...），所以我就將前面 n（vector size）-k 個元素搬到後面，再 erase 前面的 n-k 個元素，就達成旋轉的效果了。


## 效能

### Complexity 
- Time Complexity: O(N^2), vector::erase takes O(N^2)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 32 ms
- Memory Usage: 25.8 MB 
- https://leetcode.com/submissions/detail/538385799/

## Code
```cpp
class Solution {
public:
    void rotate(vector<int>& nums, int k) {
        if(k >= nums.size()) k = k % nums.size();
        
        int s = nums.size()-k;
        nums.insert(nums.end(), nums.begin(), nums.begin()+s);
        nums.erase(nums.begin(), nums.begin()+s);
    }
};
```


# Solution 2: std::reverse 
## 思路

將前面 n-k 個元素旋轉，再將後面 k 個旋轉，最後整個 vector 旋轉，就是答案了。

## 效能

### Complexity 
- Time Complexity: O(N), where vector::reverse takes O(N)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 33 ms
- Memory Usage: 24.9 MB 
- https://leetcode.com/submissions/detail/538389054/

## Code
```cpp
class Solution {
public:
    void rotate(vector<int>& nums, int k) {
        if(k >= nums.size()) k = k % nums.size();
        reverse(nums.begin(), nums.begin() + nums.size() - k); 
        reverse(nums.begin() + nums.size() - k, nums.end());
        reverse(nums.begin(), nums.end());
    }
};
```

# Solution 3: std::rotate 
## 思路

std::rotate 可以直接達成題目需要的功能。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 24 ms
- Memory Usage: 24.9 MB 
- https://leetcode.com/submissions/detail/541770229/

## Code
```cpp
class Solution {
public:
    void rotate(vector<int>& nums, int k) {
        if(k >= nums.size()) k = k % nums.size();
        std::rotate(nums.begin(), nums.begin()+nums.size()-k, nums.end());
    }
};
```