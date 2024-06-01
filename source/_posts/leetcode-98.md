---
title: 【LeetCode】98. Validate Binary Search Tree 解題報告
categories:
  - LeetCode
date: 2022-07-03 20:39:00
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/BOP24SM.jpg
---

# 98. Validate Binary Search Tree / Medium

Given the `root` of a binary tree, determine if it is a valid binary search tree (BST).

A **valid BST** is defined as follows:
- The left subtree of a node contains only nodes with keys less than the node's key.
- The right subtree of a node contains only nodes with keys greater than the node's key.
- Both the left and right subtrees must also be binary search trees.

<!-- more -->

## Example 1:
![](https://assets.leetcode.com/uploads/2020/12/01/tree1.jpg)
> Input: root = [2,1,3]
> Output: true

## Example 2:
![](https://assets.leetcode.com/uploads/2020/12/01/tree2.jpg)
> Input: root = [5,1,4,null,null,3,6]
> Output: false
> Explanation: The root node's value is 5 but its right child's value is 4.

## Constraints:
> - The number of nodes in the tree is in the range [1, 10^4].
> - -2^31 <= Node.val <= 2^31 - 1

# Solution 1: Recursive
## 思路

利用 Recursive 應該是比較直覺的做法。
但要注意的是，一顆合格的 BST，左半樹的所有節點都會小於 root 節點，所以我們必須將左半的最大值傳入；右半邊也是一樣，只是傳入的是最小值。

## 效能
### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(N)

### LeetCode Result
- Runtime: 12 ms
- Memory Usage: 21.5 MB 
- https://leetcode.com/submissions/detail/716519644/

## Code 
```cpp
class Solution {
public:
    bool validate(TreeNode* root, TreeNode* low, TreeNode* high) {
        if(!root) return true;
        
        if((high && root->val >= high->val) || 
            (low && root->val <= low->val)) return false;
        
        return validate(root->left, low, root) && validate(root->right, root, high);
        
    }
    
    bool isValidBST(TreeNode* root) {
         return validate(root, nullptr, nullptr);
    }
};
```

# Solution 2: Inorder Traversal is Sorted 
## 思路

BST 的特性之一，inorder traversal 的節點值應該會是排序狀態，所以我們將其記錄下來，檢查是否是已排序狀態。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(N)

### LeetCode Result
- Runtime: 13 ms
- Memory Usage: 21.9 MB 
- https://leetcode.com/submissions/detail/737409582/

## Code 
```cpp
class Solution {
public:
    bool isValidBST(TreeNode* root) {
        traverse(root);
        for(int i = 1; i < nums.size(); ++i) {
            if(nums[i] <= nums[i-1]) return false;
        }
        return true;
    }
    
    void traverse(TreeNode* root) {
        if(!root) return;
        traverse(root->left);
        nums.emplace_back(root->val);
        traverse(root->right);
    }
private:
    vector<int> nums;
};
```