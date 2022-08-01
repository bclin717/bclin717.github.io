---
title: 【LeetCode】437. Path Sum III 解題報告
categories:
  - LeetCode
date: 2022-08-01 13:10:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/FTjst10.jpg
---
 
# 437. Path Sum III / Medium
Given the root of a binary tree and an integer targetSum, return the number of paths where the sum of the values along the path equals targetSum.

The path does not need to start or end at the root or a leaf, but it must go downwards (i.e., traveling only from parent nodes to child nodes).

<!-- more --> 
 

## Example 1:
![](https://assets.leetcode.com/uploads/2021/04/09/pathsum3-1-tree.jpg)
> Input: root = [10,5,-3,3,2,null,11,3,-2,null,1], targetSum = 8
> Output: 3
> Explanation: The paths that sum to 8 are shown.

## Example 2:
> Input: root = [5,4,8,11,null,13,4,7,2,null,null,5,1], targetSum = 22
> Output: 3

## Constraints: 
> - The number of nodes in the tree is in the range [0, 1000].
> - -10^9 <= Node.val <= 10^9
> - -1000 <= targetSum <= 1000


# Solution 1: Recursive
## 思路 

我們可以參考 Path Sum I 與 Path Sum II 來簡單解這個題目，也就是對每個節點都去計算他們的和等於 targetSum 的有幾組。


## 效能

### Complexity 
- Time Complexity: O(N^2) 
- Space Complexity: O(N^2)

### LeetCode Result

- Runtime: 24 ms
- Memory Usage: 16 MB 
- https://leetcode.com/submissions/detail/760466681/

## Code
```cpp
class Solution {
public:
    int pathSum(TreeNode* root, int targetSum) {
        if(!root) return 0;
        return pathSumR(root, targetSum);
    }
    
    int pathSumR(TreeNode* root, int& targetSum) {
        if(!root) return 0;
        return pathSumOneR(root, targetSum, 0) +
            pathSumR(root->left, targetSum) +
            pathSumR(root->right, targetSum);
    }
    
    int pathSumOneR(TreeNode* root, int& targetSum, long curSum) {
        if(!root) return 0;
        return 
            (curSum + root->val == targetSum ? 1 : 0) +
            pathSumOneR(root->left, targetSum, curSum + root->val) +
            pathSumOneR(root->right, targetSum, curSum + root->val);
    }
};
```

# Solution 2: Prefix Sum + HashMap
## 思路 

這題可以用 Prefix Sum + HashMap 來解這個問題。
具體概念跟 560. Subarray Sum Equals K 一樣，只是在這裡，hmap[curSum] 必須要做 backtracking，不然會計算到不屬於這條路的值。

## 效能

### Complexity 
- Time Complexity: O(N) 
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 31 ms
- Memory Usage: 21.1 MB 
- https://leetcode.com/submissions/detail/762044361/

## Code
```cpp
class Solution {
public:
    int pathSum(TreeNode* root, int targetSum) {
        preorder(root, 0, targetSum);
        return count;
    }
    
    void preorder(TreeNode* root, long curSum, const int& targetSum) {
        if(!root) return;
        
        curSum += root->val;
        if(curSum == targetSum) ++count;
        count += hmap[curSum - targetSum];
        
        hmap[curSum]++;
        preorder(root->left, curSum, targetSum);
        preorder(root->right, curSum, targetSum);
        hmap[curSum]--;
    }
    
private:
    unordered_map<long, int> hmap;
    int count = 0;
};
```