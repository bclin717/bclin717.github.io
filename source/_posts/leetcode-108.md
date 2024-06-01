---
title: 【LeetCode】108. Convert Sorted Array to Binary Search Tree 解題報告
categories:
  - LeetCode
date: 2022-07-03 20:11:00
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/r4hOiFA.jpg
---

# 108. Convert Sorted Array to Binary Search Tree / Easy

Given an integer array nums where the elements are sorted in ascending order, convert it to a **height-balanced** binary search tree.

A height-balanced binary tree is a binary tree in which the depth of the two subtrees of every node never differs by more than one.

<!-- more -->

## Example 1:
![](https://assets.leetcode.com/uploads/2021/02/18/btree1.jpg)
> Input: nums = [-10,-3,0,5,9]
> Output: [0,-3,9,-10,null,5]
> Explanation: [0,-10,5,null,-3,null,9] is also accepted:
> ![](https://assets.leetcode.com/uploads/2021/02/18/btree2.jpg)

## Example 2:
![](https://assets.leetcode.com/uploads/2021/02/18/btree.jpg)
> Input: nums = [1,3]
> Output: [3,1]
> Explanation: [1,null,3] and [3,1] are both height-balanced BSTs.

## Constraints:
> - 1 <= nums.length <= 10^4
> - -10^4 <= nums[i] <= 10^4
> - nums is sorted in a strictly increasing order.

# Solution: Recursive
## 思路

因為題目說要轉換成一顆 height-balanced 的 BST，所以我們只要每次找出中間的 node，再將左邊和右邊的數字分別遞迴即可。

## 效能
### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(logN)

### LeetCode Result
- Runtime: 21 ms
- Memory Usage: 21.5 MB 
- https://leetcode.com/submissions/detail/737400040/

## Code 
```cpp
class Solution {
public:
    TreeNode* sortedArrayToBST(vector<int>& nums) {
        return buildTree(nums, 0, nums.size()-1);
    }
        
    TreeNode* buildTree(vector<int>& nums, int start, int end) {
        if(end < start) return nullptr;
        int mid = (start+end)/2;
        TreeNode* root = new TreeNode(nums[mid]);
        root->left = buildTree(nums, start, mid-1); 
        root->right = buildTree(nums, mid+1, end);
        return root;
    }
};
```