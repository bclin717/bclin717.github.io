---
title: 【LeetCode】2415. Reverse Odd Levels of Binary Tree 解題報告
categories:
  - LeetCode
date: 2022-09-21 07:48:00
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/0gSLeMG.jpg
---
 
# 2415. Reverse Odd Levels of Binary Tree / Medium

Given the root of a perfect binary tree, reverse the node values at each odd level of the tree.

- For example, suppose the node values at level 3 are [2,1,3,4,7,11,29,18], then it should become [18,29,11,7,4,3,1,2].
Return the root of the reversed tree.

A binary tree is perfect if all parent nodes have two children and all leaves are on the same level.

The level of a node is the number of edges along the path between it and the root node.
<!-- more --> 

## Example 1:
![](https://assets.leetcode.com/uploads/2022/07/28/first_case1.png)
> Input: root = [2,3,5,8,13,21,34]
> Output: [2,5,3,8,13,21,34]
> Explanation: 
> The tree has only one odd level.
> The nodes at level 1 are 3, 5 respectively, which are reversed and become 5, 3.

## Example 2:
![](https://assets.leetcode.com/uploads/2022/07/28/second_case3.png)
> Input: root = [7,13,11]
> Output: [7,11,13]
> Explanation: 
> The nodes at level 1 are 13, 11, which are reversed and become 11, 13.

## Example 3:
> Input: root = [0,1,2,0,0,0,0,1,1,1,1,2,2,2,2]
> Output: [0,2,1,0,0,0,0,2,2,2,2,1,1,1,1]
> Explanation: 
> The odd levels have non-zero values.
> The nodes at level 1 were 1, 2, and are 2, 1 after the reversal.
> The nodes at level 3 were 1, 1, 1, 1, 2, 2, 2, 2, and are 2, 2, 2, 2, 1, 1, 1, 1 after the reversal.

## Comstraints
> - The number of nodes in the tree is in the range [1, 2^14].
> - 0 <= Node.val <= 10^5
> - root is a perfect binary tree.

# Solution 1: BFS
## 思路

利用 BFS 可以很直覺的拿到每一層的 node，再將其 val swap 就好。
相關題目
[102. Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/)

[103. Binary Tree Zigzag Level Order Traversal](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/)

## 效能

### Complexity 
- Time Complexity: O(N), where N is the number of nodes
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 492 ms
- Memory Usage: 78.1 MB 
- https://leetcode.com/submissions/detail/802959974/

## Code
```cpp
class Solution {
public:
    TreeNode* reverseOddLevels(TreeNode* root) {
        int level = 1;
        queue<TreeNode*> q;
        q.push(root);
        while(!q.empty()) {
            int size = q.size();
            vector<TreeNode*> nodes;
            while(size--) {
                nodes.emplace_back(q.front()); q.pop();
                if(nodes.back()->left) q.push(nodes.back()->left);
                if(nodes.back()->right) q.push(nodes.back()->right);
            }
            
            if(level % 2 == 0) {
                int l = 0, r = nodes.size()-1;
                while(l < r) {
                    swap(nodes[l]->val, nodes[r]->val);
                    ++l; --r;
                }
            }
            ++level;
        }
        return root;
    }
};
```

# Solution 2: DFS
## 思路

利用 DFS 搭配 level 就簡單很多了，要注意的是 reverse 的 swap 應該是從外而內
所以是
> reverse(leftN->left, rightN->right, level+1);
> reverse(leftN->right, rightN->left, level+1);

## 效能

### Complexity 
- Time Complexity: O(N), where N is the number of nodes
- Space Complexity: O(N) or O(H), where H is the height of the given tree

### LeetCode Result

- Runtime: 485 ms
- Memory Usage: 71.1 MB 
- https://leetcode.com/submissions/detail/805220378/

## Code
```cpp
class Solution {
public:
    TreeNode* reverseOddLevels(TreeNode* root) {
        reverse(root->left, root->right, 2);
        return root;
    }
    
    void reverse(TreeNode* leftN, TreeNode* rightN, int level) {
        if(!leftN && !rightN) return;
        if(level % 2 == 0) swap(leftN->val, rightN->val);
        reverse(leftN->left, rightN->right, level+1);
        reverse(leftN->right, rightN->left, level+1);
    }
};
```