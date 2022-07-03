---
title: 【LeetCode】101. Symmetric Tree 解題報告
categories:
  - LeetCode
date: 2022-07-03 18:39:00
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/Vi9Knwz.jpg
---

# 101. Symmetric Tree / Easy

Given the root of a binary tree, check whether it is a mirror of itself (i.e., symmetric around its center).

<!-- more -->

## Example 1:
![](https://assets.leetcode.com/uploads/2021/02/19/symtree1.jpg)
> Input: root = [1,2,2,3,4,4,3]
> Output: true

## Example 2:
![](https://assets.leetcode.com/uploads/2021/02/19/symtree2.jpg)
> Input: root = [1,2,2,null,3,null,3]
> Output: false

## Constraints:
> - The number of nodes in the tree is in the range [1, 1000].
> - -100 <= Node.val <= 100

### Follow up: Could you solve it both recursively and iteratively?

# Solution 1: Recursive

## 思路

利用 dfs 去比較，
當兩邊的兩個節點都不存在時，回傳 true，這是對稱的，
當其中一個節點不存在而另一個存在時，回傳 false，這是非對稱的，
兩邊節點的數值必須相等，同時將 n1 的左邊與 n2 的右邊、n1 的右邊與 n2 的左邊傳入繼續做比對。

只要有一個 false 就全部都是 false。


## 效能
### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(N)

### LeetCode Result
- Runtime: 8 ms
- Memory Usage: 16.2 MB 
- https://leetcode.com/submissions/detail/717792656/

## Code 
```cpp
class Solution {
public:
    bool isSymmetric(TreeNode* root) {
        return dfs(root, root);
    }
    
    bool dfs(TreeNode* n1, TreeNode* n2) {
        if(!n1 && !n2) return true;
        if(!n1 || !n2) return false;
            
        return n1->val == n2->val && dfs(n1->left, n2->right) && dfs(n1->right, n2->left);
    }
};
```

# Solution 2: Iterative
## 思路

如果要用 Iterative 的方式做，就勢必要改用 BFS。
只是把 push 進去 queue 的順序稍作修改。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(N)

### LeetCode Result
- Runtime: 3 ms
- Memory Usage: 16.4 MB 
- https://leetcode.com/submissions/detail/717792656/

## Code 
```cpp
class Solution {
public:
    bool isSymmetric(TreeNode* root) {
        queue<TreeNode*> q;
        q.push(root);
        q.push(root);
        while(!q.empty()) {
            TreeNode* q1 = q.front(); q.pop();
            TreeNode* q2 = q.front(); q.pop();
            if(!q1 && !q2) continue;
            if(!q1 || !q2) return false;
            if(q1->val != q2->val) return false;
            q.push(q1->left); q.push(q2->right);
            q.push(q1->right); q.push(q2->left);
        }
        return true;
    }
};
```