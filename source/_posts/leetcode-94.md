---
title: 【LeetCode】94. Binary Tree Inorder Traversal 解題報告
categories:
  - LeetCode
date: 2021-09-07 18:39:00
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/txll8jw.jpg
---

# 94. Binary Tree Inorder Traversal / Easy

Given the root of a binary tree, return the inorder traversal of its nodes' values.

<!-- more -->

## Example 1:
![](https://assets.leetcode.com/uploads/2020/09/15/inorder_1.jpg)
> Input: root = [1,null,2,3]
> Output: [1,3,2]

## Example 2:
> Input: root = []
> Output: []

## Example 3:
> Input: root = [1]
> Output: [1]

## Example 4:
![](https://assets.leetcode.com/uploads/2020/09/15/inorder_5.jpg)
> Input: root = [1,2]
> Output: [2,1]

## Example 5:
![](https://assets.leetcode.com/uploads/2020/09/15/inorder_4.jpg)
> Input: root = [1,null,2]
> Output: [1,2]

## Constraints:
> - The number of nodes in the tree is in the range [0, 100].
> - -100 <= Node.val <= 100

### Follow up: Recursive solution is trivial, could you do it iteratively?

# Solution 1: Iterative (Stack)

## 思路

這題考的是對樹的基本操作，通常我們都是用遞迴達成，但 Follow up 需要你用 Iterative。
遞迴時，狀態會儲存在系統的 Stack 中，改用 Iterative 時，就必須自己生一個 Stack 來存。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(N)

### LeetCode Result
- Runtime: 0 ms
- Memory Usage: 8.3 MB 
- https://leetcode.com/submissions/detail/540392445/

## Code 
```cpp
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        if(!root) return vector<int>{};   
        stack<TreeNode*> s;
        vector<int> inorderResult;
        TreeNode* cur = root;
        while(cur != nullptr || !s.empty()) {
            while(cur) {
                s.push(cur);
                cur = cur->left;
            }
            cur = s.top(); s.pop();
            inorderResult.emplace_back(cur->val);
            cur = cur->right;
        }
        return inorderResult;
    }
};
```