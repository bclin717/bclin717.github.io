---
title: 【LeetCode】39. Combination Sum 解題報告
categories:
  - LeetCode
date: 2021-08-04 17:30:56
tags: LeetCode
photos:
    - https://i.imgur.com/YKDrfN2.jpg
---
 
# 39. Combination Sum / Medium

Given an array of distinct integers candidates and a target integer target, return a list of all unique combinations of candidates where the chosen numbers sum to target. You may return the combinations in any order.

The same number may be chosen from candidates an unlimited number of times. Two combinations are unique if the frequency of at least one of the chosen numbers is different.

It is guaranteed that the number of unique combinations that sum up to target is less than 150 combinations for the given input.

<!-- more --> 
## Example 1:
> Input: candidates = [2,3,6,7], target = 7
> Output: [[2,2,3],[7]]
> Explanation:
> 2 and 3 are candidates, and 2 + 2 + 3 = 7. Note that 2 can be used multiple times.
> 7 is a candidate, and 7 = 7.
> These are the only two combinations.

## Example 2:
> Input: candidates = [2,3,5], target = 8
> Output: [[2,2,2,2],[2,3,3],[3,5]]

## Example 3:
> Input: candidates = [2], target = 1
> Output: []

## Example 4:
> Input: candidates = [1], target = 1
> Output: [[1]]

## Example 5:
> Input: candidates = [1], target = 2
> Output: [[1,1]]



# Solution: Backtracking / DFS
## 思路
這一題可以用 Bactracking 的方式來解，以範例 [2,3,6,7], target = 7 來說， \
這個問題可以轉為，[2] [2,3,6,7], target = 7-2\
最後變成 [2,2,3] [2,3,6,7], target = 0。

根據不同的策略可以畫出一顆決策樹，例如下面這張，畫成二元樹以更好計算複雜度。策略是 skip 和 choose。
![](https://assets.leetcode-cn.com/solution-static/39/39_fig1.png)

而這張會比較接近我們的思想，7 可以被 reduced 成其他數字，我們只關注能走到 0 的 path。但像是 [2,2,3] 跟 [2,3,2] 其實是重複的，這個就必須要靠 prune 來解決。
![](https://pic.leetcode-cn.com/1598091943-hZjibJ-file_1598091940241)

我們用迴圈的 j ，透過每次傳進 function 的深度 i 來避開重複的組成。例如開頭走道 [2,3] 後就不會再走 [2,3,2] ，迴圈會直接從 [2,3,3] 開始。

## 效能

### Complexity 
- Time Complexity: O(N * 2^N) where N is the number of candidates.
- Space Complexity: O(target)

### LeetCode Result

- Runtime: 4 ms
- Memory Usage: 11 MB 
- https://leetcode.com/submissions/detail/532572046/

## Code
```cpp
class Solution {
public:
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        int n = candidates.size();
        vector<vector<int>> ans;
        vector<int> tmp;
        combination(ans, tmp, target, candidates, 0, n);
        return ans;
    }
    
    void combination(vector<vector<int>>& ans, vector<int>& tmp, int target, const vector<int>& candidates, int i, const int& n) {
        if(target == 0) {
            ans.emplace_back(tmp);
            return;
        } else if(target < 0) {
            return;
        }
        
        for(int j = i; j < n; ++j) {
            tmp.emplace_back(candidates[j]);
            combination(ans, tmp, target - candidates[j], candidates, j, n);
            tmp.pop_back();
        }
    }
};
```
