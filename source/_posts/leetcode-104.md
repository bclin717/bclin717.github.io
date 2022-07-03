---
title: 【LeetCode】104. Maximum Depth of Binary Tree 解題報告
categories:
  - LeetCode
date: 2022-07-03 20:10:00
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/XpdFZmY.jpg
---

# 104. Maximum Depth of Binary Tree / Easy

Given the root of a binary tree, return its maximum depth.

A binary tree's **maximum depth** is the number of nodes along the longest path from the root node down to the farthest leaf node.

<!-- more -->

## Example 1:
![](https://assets.leetcode.com/uploads/2020/11/26/tmp-tree.jpg)
> Input: root = [3,9,20,null,null,15,7]
> Output: 3

## Example 2:
> Input: root = [1,null,2]
> Output: 2

## Constraints:
> - The number of nodes in the tree is in the range [0, 10^4].
> - -100 <= Node.val <= 100

# Solution: Recursive

## 思路

利用 DFS 尋訪，每向下 call 一次就增加紀錄的深度，走到葉節點的時候拿目前的深度與最深的比較，不斷更新即可。

## 效能
### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(N), best case is O(logN)

### LeetCode Result
- Runtime: 13 ms
- Memory Usage: 18.8 MB 
- https://leetcode.com/submissions/detail/716475766/

## Code 
```cpp
class Solution {
public:
    int maxDepth(TreeNode* root) {
        dfs(root, 0);
        return maxD;
    }
    
    void dfs(TreeNode* root, int depth) {
        if(!root) {
            maxD = max(maxD, depth);
            return;
        }
        
        dfs(root->left, depth+1);
        dfs(root->right, depth+1);
    }
    
private:
    int maxD = 0;
};
```