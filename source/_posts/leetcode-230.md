---
title: 【LeetCode】230. Kth Smallest Element in a BST 解題報告
categories:
  - LeetCode
date: 2022-07-06 04:55:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/0rpyDy0.jpg
---
 
# 230. Kth Smallest Element in a BST / Medium

Given the root of a binary search tree, and an integer k, return the kth smallest value (1-indexed) of all the values of the nodes in the tree.

<!-- more --> 

## Example 1:
![](https://assets.leetcode.com/uploads/2021/01/28/kthtree1.jpg)
> Input: root = [3,1,4,null,2], k = 1
> Output: 1

## Example 2:
![](https://assets.leetcode.com/uploads/2021/01/28/kthtree2.jpg)
> Input: root = [5,3,6,2,4,null,null,1], k = 3
> Output: 3

## Comstraints
> - The number of nodes in the tree is n.
> - 1 <= k <= n <= 10^4
> - 0 <= Node.val <= 10^4

- Follow up: If the BST is modified often (i.e., we can do insert and delete operations) and you need to find the kth smallest frequently, how would you optimize?

# Solution 1: Traverse and store
## 思路

因為 BST 如果用 inorder 去尋訪的話會是排序過的，所以我們先用 inorder 尋訪一次，並用陣列紀錄順序。接著直接取出 index k-1 的值就好。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 20 ms
- Memory Usage: 24.2 MB 
- https://leetcode.com/submissions/detail/719058614/

## Code
```cpp
class Solution {
public:
    vector<int> nums;
    int kthSmallest(TreeNode* root, int k) {
        if(!root) return 0;
        traverse(root);
        return nums[k-1];
    }
    
    void traverse(TreeNode* root) {
        if(!root) return;
        
        traverse(root->left);
        nums.emplace_back(root->val);
        traverse(root->right);
    }
};
```


# Solution 2: Iterate with Stack
## 思路

如果改用 Stack 來尋訪，可以在走到第 k 次時直接 reture 答案。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 21 ms
- Memory Usage: 24.2 MB 
- https://leetcode.com/submissions/detail/719063735/

## Code
```cpp
class Solution {
public:
    int kthSmallest(TreeNode* root, int k) {
        stack<TreeNode*> node_stack;
        while(true) {
            while(root) {
                node_stack.push(root);
                root = root->left;
            }
            root = node_stack.top();
            node_stack.pop();
            if(--k == 0) return root->val;
            root = root->right;
        }
    }
};
```