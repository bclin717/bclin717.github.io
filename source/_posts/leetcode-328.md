---
title: 【LeetCode】328. Odd Even Linked List 解題報告
categories:
  - LeetCode
date: 2022-06-20 22:14:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/dUyGlGn.jpg
---
 
# 328. Odd Even Linked List / Medium

Given the `head` of a singly linked list, group all the nodes with odd indices together followed by the nodes with even indices, and return the reordered list.

The **first** node is considered **odd**, and the **second** node is **even**, and so on.

Note that the relative order inside both the even and odd groups should remain as it was in the input.

You must solve the problem in `O(1)` extra space complexity and `O(n)` time complexity.

<!-- more --> 
 
## Example 1:
![](https://assets.leetcode.com/uploads/2021/03/10/oddeven-linked-list.jpg)
> Input: head = [1,2,3,4,5]
> Output: [1,3,5,2,4]

## Example 2:
![](https://assets.leetcode.com/uploads/2021/03/10/oddeven2-linked-list.jpg)
> Input: head = [2,1,3,5,6,4,7]
> Output: [2,3,6,7,1,5,4]

## Constraints:
> - The number of nodes in the linked list is in the range [0, 10^4].
> - -10^6 <= Node.val <= 10^6

# Solution:
## 思路 
題目直接告訴我們要在 in-place 執行，而且時間複雜度要 O(n)，
但解法也很簡單，就是直接將基數跟偶數節點拆開。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 11 ms
- Memory Usage: 10.4 MB 
- https://leetcode.com/submissions/detail/715575475/

## Code
```cpp
class Solution {
public:
    ListNode* oddEvenList(ListNode* head) {
        if(!head || !head->next) return head;
        ListNode *head_even = head->next; 
        ListNode *head_odd = head;
        ListNode *cur_even = head_even;
        ListNode *cur_odd = head_odd;
        
        while(cur_odd->next && cur_even->next) {
            cur_odd->next = cur_even->next;
            cur_odd = cur_odd->next;
            cur_even->next = cur_odd->next;
            cur_even = cur_even->next;
        }
        
        cur_odd->next = head_even;
        return head_odd;
    }
};
```