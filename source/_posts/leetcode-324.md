---
title: 【LeetCode】324. Wiggle Sort II 解題報告
categories:
  - LeetCode
date: 2022-03-02 22:04:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/IAAUGKj.jpg
---
 
# 324. Wiggle Sort II / Medium

Given an integer array `nums`, reorder it such that `nums[0] < nums[1] > nums[2] < nums[3]...`.

You may assume the input array always has a valid answer.

<!-- more --> 

## Example 1:
> Input: nums = [1,5,1,1,6,4]
> Output: [1,6,1,5,1,4]
> Explanation: [1,4,1,5,1,6] is also accepted.

## Example 2:
> Input: nums = [1,3,2,2,3,1]
> Output: [2,3,1,3,1,2]


## Comstraints
> -  `1 <= nums.length <= 5 * 10^4`
> - `0 <= nums[i] <= 5000`
> - It is guaranteed that there will be an answer for the given input `nums`.

### Follow Up:
Can you do it in O(n) time and/or in-place with O(1) extra space?

# Solution 1: Sort and Partition
## 思路

首先將陣列複製出來排序，例如 `[1,5,1,1,6,4]`，排序後變成 `[1,1,1,4,5,6]`

我們將其切成兩堆，`[1,1,1] [4,5,6]`

然後我們輪流抽數字出來，就解決了。

## 效能

### Complexity 
- Time Complexity: O(NlogN)
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 16 ms
- Memory Usage: 17.7 MB 
- https://leetcode.com/submissions/detail/651268584/

## Code
```cpp
class Solution {
public:
    void wiggleSort(vector<int>& nums) {
        vector<int> tmp = nums;
        int n = nums.size(), k = (n + 1) / 2, j = n; 
        sort(tmp.begin(), tmp.end());
        for (int i = 0; i < n; ++i) {
            nums[i] = i % 2 ? tmp[--j] : tmp[--k];
        }
    }
};
```

# Solution 2: Quickselect + DNF (Follow Up)
## 思路

這是我在 Discussion 看到的奇異解法。

上一個解法的時間複雜度瓶頸卡在 Sort 的時候必須用到 O(NlogN)，不過其實並不需要 Sort，題目僅要求結果是 Wiggle。

以`[1,5,1,1,6,4]`為例，`[1,6,1,5,1,4]`跟`[1,4,1,5,1,6]`都是可以接受的答案。

我們可以用 Quickselect 先找出中位數，如此一來就可以在 O(N) 的時間複雜度，將陣列分成大小兩堆。

而空間複雜度的瓶頸卡在需要額外的空間編排數字，要解決這個問題，可以用 In-Place O(1) 的分堆法 The Dutch National Flag Algorithm (DNF, 荷蘭國旗演算法, a.k.a. Three-Way-Partition)

可以參考 [The Dutch national flag problem in C++ (educative.io)](https://www.educative.io/edpresso/the-dutch-national-flag-problem-in-cpp)

Related Problem: [Sort Colors - LeetCode](https://leetcode.com/problems/sort-colors/)

利用 DNF 的前提是要知道分堆的標的物（中間值），所以說要先用 nth_element (Quickselect) 找出中位值。

使用 DNF 的時候，基本上是分三堆，可是題目要 wiggle，所以說我們定義了一個 Macro A(i)：
`#define A(i) nums[(1 + 2 * i) % (n | 1)]`

實際上 A(i) 其實是：
```cpp
Accessing A(0) actually accesses nums[1].
Accessing A(1) actually accesses nums[3].
Accessing A(2) actually accesses nums[5].
Accessing A(3) actually accesses nums[7].
Accessing A(4) actually accesses nums[9].
Accessing A(5) actually accesses nums[0].
Accessing A(6) actually accesses nums[2].
Accessing A(7) actually accesses nums[4].
Accessing A(8) actually accesses nums[6].
Accessing A(9) actually accesses nums[8].
```

我們不是順序存取，而是照個 Wiggle 的順序存取，所以分堆時就可以分成 Wiggle 的樣子。非常有趣的想法。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 11 ms
- Memory Usage: 17.4 MB 
- https://leetcode.com/submissions/detail/651871822/

## Code
```cpp
class Solution {
public:
    void wiggleSort(vector<int>& nums) {
        #define A(i) nums[(1 + 2 * i) % (n | 1)]

        int n = nums.size(), i = 0, j = 0, k = n - 1;
        auto midptr = nums.begin() + n / 2;
        nth_element(nums.begin(), midptr, nums.end());
        int mid = *midptr;
        while (j <= k) {
            if (A(j) > mid) swap(A(i++), A(j++));
            else if (A(j) < mid) swap(A(j), A(k--));
            else ++j;
        }
    }
};
```
