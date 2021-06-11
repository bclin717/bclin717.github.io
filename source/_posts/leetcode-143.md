---
title: 【LeetCode】143. Reorder List 解題報告
categories:
  - LeetCode
date: 2021-06-11 21:09:56
tags: LeetCode
top_image: https://i.imgur.com/yxiRgJr.jpg
---

# 143. Reorder List / Medium

You are given the head of a singly linked-list. The list can be represented as:

> L0 → L1 → … → Ln - 1 → Ln

Reorder the list to be on the following form:

> L0 → Ln → L1 → Ln - 1 → L2 → Ln - 2 → … 

You may not modify the values in the list's nodes. Only nodes themselves may be changed.

 
## Example 1:
![](https://assets.leetcode.com/uploads/2021/03/04/reorder1linked-list.jpg)
> Input: head = [1,2,3,4] \
> Output: [1,4,2,3]
## Example 2:
![](https://assets.leetcode.com/uploads/2021/03/09/reorder2-linked-list.jpg)
> Input: head = [1,2,3,4,5] \
> Output: [1,5,2,4,3]
 

## Constraints:
> The number of nodes in the list is in the range [1, 5 * 104]. \
1 <= Node.val <= 1000


# 思路

這一題乍看複雜，其實不然。實際上可以被分成三個步驟：找中點、翻轉、合併。 \
由於題目要求要在原來的 List 上進行操作，我們不能存到另一個新的 List。\
首先找到整個 List 的中點，將 List 從中點斷開，形成兩個獨立的 List。
接著翻轉（Reverse）後半部的 List，最後再將後半部 List 的節點間隔地插入到前半部 List。

前兩個步驟可以參考另外兩題：
1. 876. Middle of the Linked List
2. 206. Reverse Linked List

## 尋找中點
使用快慢指標，快的指標走兩步，慢的走一步，當快指標到 List 的尾巴時，慢的會剛好在中間。

## 翻轉 / 合併 List
好像沒什麼特別的，畫個圖就能理解了。很簡單的指標操作。

# 效能

## Complexity 
- Time Complexity: O(n)
- Space Complexity: O(1)

## LeetCode Result

- Runtime: 32 ms
- Memory Usage: 17.8 MB
- https://leetcode.com/submissions/detail/505846393/

# Code 
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    void reorderList(ListNode* head) {
        if(!head || !head->next || !head->next->next) return;
        
        // 1. Find the Middle Node
        ListNode *fast = head, *slow = head;
        while(fast->next && fast->next->next) {
            fast = fast->next->next;
            slow = slow->next;
        }
        
        // 2. Reverse the Second List
        ListNode *cur = slow->next, *pre = NULL;
        slow->next = NULL;
        while (cur != nullptr) {
            ListNode *next = cur->next;
            cur->next = pre;
            pre = cur;
            cur = next;
        }
        
        // 3. Merge Two Lists
        ListNode *t1, *t2;
        while(head != nullptr && pre != nullptr) {
            t1 = head->next;
            t2 = pre->next;
            head->next = pre;
            pre->next = t1;
            head = t1;
            pre = t2;
        }
        
    }
};
```
