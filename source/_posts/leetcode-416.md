---
title: 【LeetCode】416. Partition Equal Subset Sum 解題報告
categories:
  - LeetCode
date: 2022-08-01 14:54:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/rwDjfl4.jpg
---
 
# 416. Partition Equal Subset Sum / Medium
Given a non-empty array nums containing only positive integers, find if the array can be partitioned into two subsets such that the sum of elements in both subsets is equal.
 
<!-- more --> 

## Example 1:
> Input: nums = [1,5,11,5]
> Output: true
> Explanation: The array can be partitioned as [1, 5, 5] and [11].

## Example 2:
> Input: nums = [1,2,3,5]
> Output: false
> Explanation: The array cannot be partitioned into equal sum subsets.

## Constraints: 
> - 1 <= nums.length <= 200
> - 1 <= nums[i] <= 100

# Solution 1: DP with Memorization
## 思路 

對於這題，首先我們可以只用 DFS 暴力解，但會超時。
每一次遞迴，都要決定要不要選目前這個 nums[i]，要選的話，targetSum 就會被減去 nums[i]。
也就是說每一次都會有兩條路走，會是 O(2^N)。
為此，我們用 DP 記錄下答案來加速，DP[i][j] 代表從 i 開始否能解掉 halfSum。

## 效能

### Complexity 
- Time Complexity: O(MN), where M is the length of nums and N is the biggest halfSum
- Space Complexity: O(MN)

### LeetCode Result

- Runtime: 283 ms
- Memory Usage: 348.7 MB 
- https://leetcode.com/submissions/detail/762130682/

## Code
```cpp
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        const int M = 201;
        const int N = 10001;
        dp = vector<vector<int>>(M, vector<int>(N, -1));
        
        int target = accumulate(nums.begin(), nums.end(), 0);
        if(target & 1) return false;
        return subsetSum(nums, target/2, 0);
    }
    
    bool subsetSum(vector<int>& nums, int targetSum, int i) {
        if(targetSum == 0) return true;
        if(i >= nums.size() || targetSum < 0) return false;
        if(dp[i][targetSum] != -1) return dp[i][targetSum];
        return dp[i][targetSum] = 
                (subsetSum(nums, targetSum - nums[i], i+1)
                || subsetSum(nums, targetSum, i+1));
    }
private:
    vector<vector<int>> dp;
};
```

# Solution 2: DP - Tabulation
## 思路 

這個思路類似了 Coin Change 的做法。
dp[sum] 代表是否可以解掉 sum。
我們假設手上有 1, 5, 5, 11 這些硬幣，要解掉 11
那就可以用 11 可以解掉嗎？例如 11 - 5 = 6，而 6 是可以解掉的，那就代表是可以解掉的（因為 6 可解而我們又拿到 5）。

## 效能

### Complexity 
- Time Complexity: O(N*sum)
- Space Complexity: O(sum)

### LeetCode Result

- Runtime: 283 ms
- Memory Usage: 348.7 MB 
- https://leetcode.com/submissions/detail/762130682/

## Code
```cpp
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        int totalSum = accumulate(begin(nums), end(nums), 0), halfSum = totalSum / 2;
        if(totalSum & 1) return false;
        vector<bool> dp(halfSum+1, false);
        dp[0] = true;                              
        for(int num : nums) 
            for(int j = halfSum; j >= num; j--)   
                if(dp[j - num])                 
                    dp[j] = true;  
            
        return dp[halfSum];
    }
};
```