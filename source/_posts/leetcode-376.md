---
title: 【LeetCode】376. Wiggle Subsequence 解題報告
categories:
  - LeetCode
date: 2022-07-04 07:16:00
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/CjgJrpB.jpg
---

# 376. Wiggle Subsequence / Medium

A **wiggle sequence** is a sequence where the differences between successive numbers strictly alternate between positive and negative. The first difference (if one exists) may be either positive or negative. A sequence with one element and a sequence with two non-equal elements are trivially wiggle sequences.

- For example, `[1, 7, 4, 9, 2, 5]` is a **wiggle sequence** because the differences `(6, -3, 5, -7, 3)` alternate between positive and negative.
- In contrast, `[1, 4, 7, 2, 5]` and `[1, 7, 4, 5, 5]` are not wiggle sequences. The first is not because its first two differences are positive, and the second is not because its last difference is zero.
A **subsequence** is obtained by deleting some elements (possibly zero) from the original sequence, leaving the remaining elements in their original order.

Given an integer array nums, return the length of the longest **wiggle subsequence** of `nums`.

<!-- more -->

## Example 1:
> Input: nums = [1,7,4,9,2,5]
> Output: 6
> Explanation: The entire sequence is a wiggle sequence with differences (6, -3, 5, -7, 3).

## Example 2:
> Input: nums = [1,17,5,10,13,15,10,5,16,8]
> Output: 7
> Explanation: There are several subsequences that achieve this length.
> One is [1, 17, 10, 13, 10, 16, 8] with differences (16, -7, 3, -3, 6, -8).

## Example 3:
> Input: nums = [1,2,3,4,5,6,7,8,9]
> Output: 2

## Constraints:
> - 1 <= nums.length <= 1000
> - 0 <= nums[i] <= 1000

# Solution 1: Simply DP
## 思路

遇到 subsequence 的問題，很常會需要使用 DP 來解。
由於 wiggle sequence 指的是增減交錯的序列，我們用兩個 dp 陣列來存，一個是上升序列，一個是下降序列。
如果是前後相減是正數，那 up 的值就會是 down 的值加 1；反之，若是負數，down 的值就會是 up 的值加 1，這個增減反轉的位置代表 wiggle sequence 變長了。
最後我們去看雙方誰比較多，就是答案了。要注意的是初始值是 1，因為一個也算是子序列。

## 效能
### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(N)

### LeetCode Result
- Runtime: 5 ms
- Memory Usage: 7.3 MB 
- https://leetcode.com/submissions/detail/737786680/

## Code 
```cpp
class Solution {
public:
    int wiggleMaxLength(vector<int>& nums) {
        if(nums.size() < 2) return nums.size();
        int n = nums.size();
        vector<int> up(n, 1);
        vector<int> down(n, 1);
        for(int i = 1; i < n; ++i) {
            if(nums[i] > nums[i-1]) {
                up[i] = down[i-1] + 1;
                down[i] = down[i-1];
            } else if(nums[i] < nums[i-1]) {
                up[i] = up[i-1];
                down[i] = up[i-1] + 1;
            } else {
                down[i] = down[i-1];
                up[i] = up[i-1];
            }
        }
        return max(down[n-1], up[n-1]);
    }
};
```

# Solution 2: DP with Space O(1)
## 思路

仔細觀察就會發現，其實我們不需要記錄所有的值，只需要知道 up 和 down 兩個 dp 陣列最後的值就能更新到底了。
所以我們改用兩個變數來儲存，過程幾乎不變。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(1)

### LeetCode Result
- Runtime: 3 ms
- Memory Usage: 7 MB 
- https://leetcode.com/submissions/detail/737787219/

## Code 
```cpp
class Solution {
public:
    int wiggleMaxLength(vector<int>& nums) {
        if(nums.size() < 2) return nums.size();
        int n = nums.size();
        int down = 1;
        int up = 1;
        for(int i = 1; i < n; ++i) {
            if(nums[i] > nums[i-1]) {
                up = down + 1;
            } else if(nums[i] < nums[i-1]) {
                down = up + 1;
            } 
        }
        return max(down, up);
    }
};
```

# Solution 3: Greedy
## 思路

我們可以透過觀察下列兩個範例資料來理解 Greedy。

- Example 1 => nums = `[1, 2, 3, 4, 5, 6, 7, 8, 9 ]`
![](https://assets.leetcode.com/users/images/647dfe5d-e279-42a1-af4d-325d6fe32d8e_1656808266.1378062.png)

- Example 2 => nums = `[1, 17, 5, 10, 13, 15, 10, 5, 16, 8 ]`
![](https://assets.leetcode.com/users/images/d15aa922-68f7-448a-b209-8c4603803397_1656808272.9690814.png)

不管 input 長什麼樣子，我們只要不斷去找最高與最低點，也就是序列中，增減反轉的位置，就可以得出最長的 wiggle sequence。
也就是說，如果前面差值是正的，我們就要找到目前差值為負的地方，反之亦然。
要注意的是，開頭與結尾也算是反轉點，也就是說，如果一開始的差值（`nums[1]-nums[0]`）不為零，那開頭就是一個反轉點，而結尾也必然一個反轉點。
所以說， preDiff 必然為 1 （開頭差值為零）或 2（開頭與結尾都算反轉）。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(1)

### LeetCode Result
- Runtime: 0 ms
- Memory Usage: 7 MB 
- https://leetcode.com/submissions/detail/737788274/

## Code 
```cpp
class Solution {
public:
    int wiggleMaxLength(vector<int>& nums) {
        if(nums.size() < 2) return nums.size();
        int preDiff = nums[1] - nums[0];
        int count = preDiff != 0 ? 2 : 1;
        for(int i = 2; i < nums.size(); ++i) {
            int diff = nums[i] - nums[i-1];
            if((diff > 0 && preDiff <= 0) || (diff < 0 && preDiff >= 0)) {
                ++count;
                preDiff = diff;
            }
        }
        return count;
    }
};
```