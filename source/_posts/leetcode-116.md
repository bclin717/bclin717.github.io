---
title: 【LeetCode】116. Populating Next Right Pointers in Each Node 解題報告
categories:
  - LeetCode
date: 2022-01-11 22:26:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/IT3aDCl.jpg
---
 
# 116. Populating Next Right Pointers in Each Node / Medium

You are given a perfect binary tree where all leaves are on the same level, and every parent has two children. The binary tree has the following definition:

> struct Node {
>   int val;
>   Node *left;
>   Node *right;
>   Node *next;
> }

Populate each next pointer to point to its next right node. If there is no next right node, the next pointer should be set to NULL.

Initially, all next pointers are set to NULL.

<!-- more --> 

## Example 1:
![](https://assets.leetcode.com/uploads/2019/02/14/116_sample.png)
> Input: root = [1,2,3,4,5,6,7]
> Output: [1,#,2,3,#,4,5,6,7,#]
> Explanation: Given the above perfect binary tree (Figure A), your function should populate each next pointer to point to its next right node, just like in Figure B. The serialized output is in level order as connected by the next pointers, with '#' signifying the end of each level.

## Example 2:
> Input: root = []
> Output: []

## Constraints: 
> - The number of nodes in the tree is in the range [0, 2^12 - 1].
> - -1000 <= Node.val <= 1000


# Solution 1: Level Order Traversal / BFS
## 思路

從題目來看，感覺可以一層一層的去尋訪樹，這就要請出 BFS 了。
寫法基本上和尋訪相同，只有兩個地方不同：
### 一、要連結 next 指標
可以透過 q.front() 拿出 Queue 最前面的節點，在這裡指的就是同階層中的右節點。
還要檢查目前的節點是不是階層中的 last，也就是最後一個節點。


### 二、要知道目前的節點是否為同層的最後一個
每當遇到最後一個節點時，我們已經將同層中所有的子節點放進 Queue 了，
這時就可以透過 q.back() 拿出 Queue 的最後一個節點，也就是我們要找的節點啦。



## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 16 ms
- Memory Usage: 17.2 MB 
- https://leetcode.com/submissions/detail/617662884/

## Code
```cpp
class Solution {
public:
    Node* connect(Node* root) {
        if(root == NULL) return NULL;
        queue<Node*> q;
        Node *last, *curr, *next;
        q.push(root);
        last = q.back();
        while(!q.empty()) {
            curr = q.front();
            q.pop();
            
            if(curr->left != NULL) q.push(curr->left);
            if(curr->right != NULL) q.push(curr->right);
            
            if(!q.empty() && curr != last) curr->next = q.front();
            else if(curr == last) last = q.back();
        }
        return root;    
    }
};
```

# Solution 2: Recursive
## 思路

這題可以用遞迴或是迴圈解，我個人偏好遞迴，因為他非常美，又很快。
基本上按照題目的說法，把左節點和右節點連在一起就好。
過程中檢查父節點是不是有 next，有的話就把現在的右節點，與右邊節點的左子節點連在一起。
聽起來有點拗口，但程式碼非常漂亮。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(N) (The stack is counted)

### LeetCode Result

- Runtime: 12 ms
- Memory Usage: 18.6 MB 
- https://leetcode.com/submissions/detail/617649043/

## Code
```cpp
class Solution {
public:
    Node* connect(Node* root) {
        if(!root) return nullptr;
        
        if(root->left) {
            root->left->next = root->right;
            if(root->next) {
                root->right->next = root->next->left;
            }
        }
        
        connect(root->left);
        connect(root->right);
        return root;
    }
};
```

