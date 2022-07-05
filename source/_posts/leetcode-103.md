---
title: 【LeetCode】103. Binary Tree Zigzag Level Order Traversal 解題報告
categories:
  - LeetCode
date: 2022-07-04 21:33:00
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/JnSaVL6.jpg
---

# 103. Binary Tree Zigzag Level Order Traversal / Medium

Given the `root` of a binary tree, return the zigzag level order traversal of its nodes' values. (i.e., from left to right, then right to left for the next level and alternate between).

<!-- more -->

## Example 1:
![](https://assets.leetcode.com/uploads/2021/02/19/tree1.jpg)
> Input: root = [3,9,20,null,null,15,7]
> Output: [[3],[20,9],[15,7]]

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

這一題跟 [102. Binary Tree Level Order Traversal](https://bclin.tw/2022/07/04/leetcode-102/) 很像，其實就是一樣的做法，只是

## 效能
### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(N)

### LeetCode Result
- Runtime: 3 ms
- Memory Usage: 11.8 MB 
- https://leetcode.com/submissions/detail/718019866/

## Code 
```cpp
class Solution {
public:
    vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
        queue<TreeNode*> q;
        vector<vector<int>> sol(1, vector<int>());
        if(root == nullptr) return vector<vector<int>>();
        TreeNode* rightMost = root;
        q.push(root);
        int cnt = 0;
        while(!q.empty()) {
            TreeNode* cur = q.front(); q.pop();
            if(cur->left) q.push(cur->left);
            if(cur->right) q.push(cur->right);
            
            sol.back().emplace_back(cur->val);
            if(cur == rightMost) {
                ++cnt;
                if(cnt%2 == 0) reverse(sol.back().begin(), sol.back().end());
                rightMost = q.back();
                if(!q.empty())
                    sol.emplace_back(vector<int>());
            }
        }
        return sol;
    }
};
```
