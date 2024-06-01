---
title: 【LeetCode】122. Best Time to Buy and Sell Stock II 解題報告
categories:
  - LeetCode
date: 2021-08-20 23:38:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/iRzHyXP.jpg
---
 
# 122. Best Time to Buy and Sell Stock II / Easy

You are given an array prices where prices[i] is the price of a given stock on the ith day.

Find the maximum profit you can achieve. You may complete as many transactions as you like (i.e., buy one and sell one share of the stock multiple times).

Note: You may not engage in multiple transactions simultaneously (i.e., you must sell the stock before you buy again).
<!-- more --> 


## Example 1:
> Input: prices = [7,1,5,3,6,4]
> Output: 7
> Explanation: Buy on day 2 (price = 1) and sell on day 3 (price = 5), profit = 5-1 = 4.
> Then buy on day 4 (price = 3) and sell on day 5 (price = 6), profit = 6-3 = 3.

## Example 2:
> Input: prices = [1,2,3,4,5]
> Output: 4
> Explanation: Buy on day 1 (price = 1) and sell on day 5 (price = 5), profit = 5-1 = 4.
> Note that you cannot buy on day 1, buy on day 2 and sell them later, as you are engaging multiple transactions at the same time. You must sell before buying again.

## Example 3:
> Input: prices = [7,6,4,3,1]
> Output: 0
> Explanation: In this case, no transaction is done, i.e., max profit = 0.

## Constraints: 
> - 1 <= prices.length <= 3 * 10^4
> - 0 <= prices[i] <= 10^4


# Solution: 
## 思路

上漲行情，如果完美的買低賣高，就是一直賺，至於下跌的狀況就不管他，全部加起來就是答案了。


## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 4 ms
- Memory Usage: 13 MB 
- https://leetcode.com/submissions/detail/538368359/

## Code
```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int profit = 0;
        for(int i = 1; i < prices.size(); ++i) {
            if(prices[i] > prices[i-1]) {
                profit += (prices[i] - prices[i-1]);
            }
        }
        return profit;
    }
};
```