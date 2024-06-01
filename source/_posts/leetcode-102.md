---
title: 【LeetCode】102. Binary Tree Level Order Traversal 解題報告
categories:
  - LeetCode
date: 2022-07-04 21:20:00
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/Jm1NSzr.jpg
---

# 102. Binary Tree Level Order Traversal / Medium

Given the `root` of a binary tree, return the level order traversal of its nodes' values. (i.e., from left to right, level by level).

<!-- more -->

## Example 1:
![](https://assets.leetcode.com/uploads/2021/02/19/tree1.jpg)
> Input: root = [3,9,20,null,null,15,7]
> Output: [[3],[9,20],[15,7]]

## Example 2:
> Input: root = [1]
> Output: [[1]]

## Example 3:
> Input: root = []
> Output: []

## Constraints:
> - The number of nodes in the tree is in the range `[0, 2000]`.
> - `-1000 <= Node.val <= 1000`

# Solution:

## 思路

這種橫向尋訪的題目，利用 BFS 來做會比較容易。
每次都要紀錄最右邊的節點，
而最右邊的節點則是，當目前的節點已經是之前紀錄最右邊的節點時，queue 最後面的就是下個最右邊的節點。

## 效能
### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(N)

### LeetCode Result
- Runtime: 0 ms
- Memory Usage: 12.6 MB 
- https://leetcode.com/submissions/detail/617539905/

## Code 
```cpp
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        if(!root) return vector<vector<int>>();
        vector<vector<int>> sol(1, vector<int>());
        TreeNode* rightMost = root;
        queue<TreeNode*> q;
        q.push(root);
        while(!q.empty()) {
            TreeNode* cur = q.front(); q.pop();
            sol.back().emplace_back(cur->val);
            if(cur->left) q.push(cur->left);
            if(cur->right) q.push(cur->right);
            if(cur == rightMost && !q.empty()) {
                sol.emplace_back(vector<int>());
                rightMost = q.back();
            }
        }
        return sol;
    }
};
```
