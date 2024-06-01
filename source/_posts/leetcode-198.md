---
title: 【LeetCode】198. House Robber 解題報告
categories:
  - LeetCode
date: 2022-06-28 20:53:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/UqUdXzh.jpg
---
 
# 198. House Robber / Medium

You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed, the only constraint stopping you from robbing each of them is that adjacent houses have security systems connected and it will automatically contact the police if two adjacent houses were broken into on the same night.

Given an integer array nums representing the amount of money of each house, return the maximum amount of money you can rob tonight without alerting the police.

<!-- more --> 


## Example 1:
> Input: nums = [1,2,3,1]
> Output: 4
> Explanation: Rob house 1 (money = 1) and then rob house 3 (money = 3).
> Total amount you can rob = 1 + 3 = 4.

## Example 2:
> Input: nums = [2,7,9,3,1]
> Output: 12
> Explanation: Rob house 1 (money = 2), rob house 3 (money = 9) and rob house 5 (money = 1).
> Total amount you can rob = 2 + 9 + 1 = 12.


## Constraints: 
> - 1 <= nums.length <= 100
> - 0 <= nums[i] <= 400


# Solution: DP
## 思路

記得第一次遇到這題是在 CPE，當時只懂暴力解。如今再遇已是 LeetCode 與面試，利用 DP 優雅搞定。
不能搶連續的房子，也就是說，我們要決定是要搶這間，還是前一間。
利用 DP，我們紀錄到某房子時能搶得最多多少錢。

例如範例 [2,1,3,1]，dp[0] 就是 2 元，dp[1] 也是 2 元，因為搶第一間比較值錢。
於是狀態轉移方程式就會是 dp[i] + max(dp[i-1], dp[i-2]+nums[i])
也就是說，我們拿 搶到前一家最多能搶的錢 與 搶到前前家再搶這家的錢 來比較看誰多，就是搶到現在最多能搶多少錢。

## 效能

### Complexity 
- Time Complexity: O(N), where N is the length of nums
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 0 ms
- Memory Usage: 7.7 MB 
- https://leetcode.com/submissions/detail/732206655/

## Code
```cpp
class Solution {
public:
    int rob(vector<int>& nums) {
        int n = nums.size();
        if(n == 1) return nums[0];
        vector<int> dp(n, 0);
        dp[0] = nums[0];
        dp[1] = max(dp[0], nums[1]);
        for(int i = 2; i < n; ++i) {
            dp[i] = max(dp[i-1], dp[i-2]+nums[i]);
        }
        return dp[n-1];
    }
};
```