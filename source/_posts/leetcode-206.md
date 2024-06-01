---
title: 【LeetCode】206. Reverse Linked List 解題報告
categories:
  - LeetCode
date: 2022-06-19 22:23:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/RxfMmie.jpg
---
 
# 206. Reverse Linked List / Easy

Given the `head` of a singly linked list, reverse the list, and return the `reversed` list.

<!-- more --> 

## Example 1:
![](https://assets.leetcode.com/uploads/2021/02/19/rev1ex1.jpg)
> Input: head = [1,2,3,4,5]
> Output: [5,4,3,2,1]

## Example 2:
![](https://assets.leetcode.com/uploads/2021/02/19/rev1ex2.jpg)
> Input: head = [1,2]
> Output: [2,1]

## Example 3:
> Input: head = []
> Output: []


## Constraints: 
> - The number of nodes in the list is the range [0, 5000].
> - -5000 <= Node.val <= 5000

# Solution 
## 思路

如果要 in-place 反轉整個 linked list，就必須要每一步都將 node 之間的指標反轉。
至少會需要三個指標，一個指向前面，一個指向後面，另一個則是目前節點的指標。
直接看程式碼，很直白。

## 效能

### Complexity 
- Time Complexity: O(N), where N is the length of the linked list
- Space Complexity: O(1)

### LeetCode Result
- Runtime: 11 ms
- Memory Usage: 8.3 MB
- https://leetcode.com/submissions/detail/711920523/

## Code
```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if(!head) return nullptr;
        if(!head->next) return head;
        
        ListNode *prev = nullptr, *next = head->next, *curr = head;
        while(curr) {
            next = curr->next;
            curr->next = prev;
            prev = curr;
            curr = next;
        }
        return prev;
    }
};
```
