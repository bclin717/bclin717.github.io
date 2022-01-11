---
title: Binary Tree Traversal 的方法
categories:
  - Algorithm
date: 2022-01-11 21:23:00
hidden: false
photos:
    - https://i.imgur.com/uv1SRXI.png
---

# Binary Tree Traversal

眾所周知，Binary Tree Traversal 有三種方式，In-Order（中序法）、Pre-Order（前序法）與 Post-Order（後序法）。他們的差異主要是尋訪時，父節點相對於子節點的順序。

In-Order:   左中右
Pre-Order:  中左右
Post-Order: 左右中

例如，我們有一顆這樣的樹。

![](https://i.imgur.com/uiR7hHz.png)

順序分別是：
In-Order:   [9,3,2,4,5,7,1,11,7]
Pre-Order:  [4,2,3,9,1,5,7,7,11]
Post-Order: [9,3,2,7,5,11,7,1,4]

會寫程式的人，大部分都知道如何以遞迴來解，但用迴圈來解，第一時間大家可能都會卡住。
其實利用遞迴，也是使用作業系統的 Stack Segment（堆疊段）來儲存資訊。要改成用迴圈尋訪二元樹，就得用 Stack 把節點資訊儲存起來。

# In-Order（中序法）
[94. Binary Tree Inorder Traversal](https://leetcode.com/problems/binary-tree-inorder-traversal/)

先往左走到底，邊走邊紀錄節點資訊到 Stack 中，到底之後，我們拿出堆疊最上面的節點，拿出值，並往右節點前進一步，繼續向左。

## Recursive
```cpp
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        traversal(root);
        return sol;
    }
    
    void traversal(TreeNode* root) {
        if(!root) return;
        traversal(root->left);
        sol.emplace_back(root->val);
        traversal(root->right);
    }
    
private:
    vector<int> sol;
};
```

## Iterative
```cpp
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> sol;
        if(root == nullptr) return sol;
        stack<TreeNode*> s;
        TreeNode* cur = root;

        while(cur || !s.empty()) {
            if(cur) {
                s.push(cur);
                cur = cur->left;
            } else {
                cur = s.top(); s.pop();
                sol.emplace_back(cur->val);
                cur = cur->right;
            }
        }
        return sol;
    }
};
```

# Pre-Order（前序法）
[144. Binary Tree Preorder Traversal](https://leetcode.com/problems/binary-tree-preorder-traversal/)

從 Stack 頂部先把父節點拿出來，儲存值。接著把右節點放入 Stack，再把左節點放入 Stack。
下一次就會從左節點先拿出來當作子樹的父節點了。

## Recursive
```cpp
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        traversal(root);
        return sol;
    }
    
    void traversal(TreeNode* root) {
        if(!root) return;
        
        sol.emplace_back(root->val);
        traversal(root->left);
        traversal(root->right);
    }
private:
    vector<int> sol;
};
```

## Iterative
```cpp
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> sol;
        if(root == nullptr) return sol;
        
        stack<TreeNode*> s;
        s.push(root);
        while(!s.empty()) {
            TreeNode* cur = s.top(); s.pop();
            sol.emplace_back(cur->val);
            
            if(cur->right) s.push(cur->right);
            if(cur->left) s.push(cur->left);
        }
        return sol;
    }
};
```


# Post-Order（後序法）
[145. Binary Tree Postorder Traversal](https://leetcode.com/problems/binary-tree-postorder-traversal/)

先把父節點從堆疊頂拿出來（看就好，不要 pop），將其右左子節點分別放入 Stack，並且將指標設成 nullptr。下一次迴圈，拿出來的就會是左節點。

最後，如果拿出來的節點左右都已經是空的，代表左右節點都已經讀取完畢，我們可以放心的把父節點 pop 出 Stack，將值存起來。

## Recursive
```cpp
class Solution {
public:
    vector<int> postorderTraversal(TreeNode* root) {
        traversal(root);
        return sol;
    }
    
    void traversal(TreeNode* root) {
        if(!root) return;
        
        traversal(root->left);
        traversal(root->right);
        sol.emplace_back(root->val);
    }
private:
    vector<int> sol;
};
```

## Iterative
```cpp
class Solution {
public:
    vector<int> postorderTraversal(TreeNode* root) {
        vector<int> sol;
        if(root == nullptr) return sol;
        
        stack<TreeNode*> s;
        s.push(root);
        TreeNode* cur = root;
        
        while(!s.empty()) {
            cur = s.top(); 
            if(!cur->left &&& !cur->right) {
                s.pop();
                sol.emplace_back(cur->val);
            }
            
            if(cur->right) {
                s.push(cur->right);
                cur->right = nullptr;
            }
            
            if(cur->left) {
                s.push(cur->left);
                cur->left = nullptr;
            }
        }
        return sol;
    }
};
```