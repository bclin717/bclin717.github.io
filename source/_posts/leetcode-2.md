---
title: 【LeetCode】2. Add Two Numbers 解題報告
categories:
  - LeetCode
date: 2022-06-20 22:00:00
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/WNMumYh.jpg
---

# 2. Add Two Numbers / Medium

You are given two **non-empty** linked lists representing two non-negative integers. The digits are stored in reverse order, and each of their nodes contains a single digit. Add the two numbers and return the sum as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

<!-- more -->

## Example 1:
![](https://assets.leetcode.com/uploads/2020/10/02/addtwonumber1.jpg)
> Input: l1 = [2,4,3], l2 = [5,6,4]
> Output: [7,0,8]
> Explanation: 342 + 465 = 807.

## Example 2:
> Input: l1 = [0], l2 = [0]
> Output: [0]

## Example 3:
> Input: l1 = [9,9,9,9,9,9,9], l2 = [9,9,9,9]
> Output: [8,9,9,9,0,0,0,1]

## Constraints:
> - The number of nodes in each linked list is in the range `[1, 100]`.
> - `0 <= Node.val <= 9`
> - It is guaranteed that the list represents a number that does not have leading zeros.

# Solution 

## 思路

想法其實很簡單，就跟加法器一樣，兩個值相加，算出是否需要進位，放回到其中一條 list。
如果其中一條沒了而另一條還有，就繼續加。最後要記得判斷是否還有 carry，有的話就要創一個新的節點。

## 效能

### Complexity 
- Time Complexity: O(M+N), where M and N is the length of list1 and list2 respectively.
- Space Complexity: O(1)

### LeetCode Result
- Runtime: 40 ms
- Memory Usage: 70.9 MB 
- https://leetcode.com/submissions/detail/726724292/

## Code 
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
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode *dummy = new ListNode(0);
        dummy->next = l1;
        ListNode *p = l1, *q = l2, *pre;
        int carry = 0;
        while(p && q) {
            int x = p ? p->val : 0;
            int y = q ? q->val : 0;
            int sum = carry + x + y;
            carry = sum / 10;
            p->val = sum % 10;
            pre = p;
            p = p->next;
            q = q->next;
        }
        
        if(q) {
            pre->next = q;
            p = pre->next;
        }
        
        while(p) {
            p->val = p->val + carry;
            carry = p->val / 10;
            p->val = p->val % 10;
            pre = p;
            p = p->next;
        }
        
        if(carry > 0) pre->next = new ListNode(1);
        return dummy->next;
    }
};
```