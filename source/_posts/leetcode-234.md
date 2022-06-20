---
title: 【LeetCode】234. Palindrome Linked List 解題報告
categories:
  - LeetCode
date: 2022-06-20 11:01:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/TcVPFjw.jpg
---
 
# 234. Palindrome Linked List / Easy

Given the `head` of a singly linked list, return `true` if it is a palindrome.

<!-- more --> 

## Example 1:
![](https://assets.leetcode.com/uploads/2021/03/03/pal1linked-list.jpg)
> Input: head = [1,2,2,1]
> Output: true

## Example 2:
![](https://assets.leetcode.com/uploads/2021/03/03/pal2linked-list.jpg)
> Input: head = [1,2]
> Output: false


## Constraints: 
> - The number of nodes in the list is in the range [1, 10^5].
> - 0 <= Node.val <= 9

- Follow up: Could you do it in O(n) time and O(1) space?

# Solution 
## 思路

比較簡單的方法是，先將 list 的數字放入一個 vector，再用其他方式判斷是不是迴文。
如果要在 in-place 做的話，思維如下
1. 將 list 從中間斷開
2. reverse 後半部的 list
3. 判斷兩個 list 是否相等

關於第一步，要找出 list 的中點可以使用快慢指標，當快指標到達尾部時，慢指標會在中點。
第二步則可以參考 [Leetcode 206. Reverse Linked List](https://bclin.tw/2022/06/19/leetcode-206)

## 效能
### Complexity 
- Time Complexity: O(N), where N is the length of the linked list
- Space Complexity: O(1)

### LeetCode Result
- Runtime: 241 ms
- Memory Usage: 114 MB
- https://leetcode.com/submissions/detail/726386248/

## Code
```cpp
class Solution {
public:
    bool isPalindrome(ListNode* head) {
        ListNode *fast = head, *slow = head;
        ListNode *curr = head, *head2 = nullptr;
        
        while(fast->next && fast->next->next) {
            slow = slow->next;
            fast = fast->next->next;
        }
        
        head2 = slow->next;
        slow->next = nullptr;
        
        head2 = reverseList(head2);
        return isPalindrome(head, head2);
    }
    
    ListNode* reverseList(ListNode* head) {
        if(!head) return nullptr;
        if(!head->next) return head;
        
        ListNode *prev = nullptr, *curr = head, *next = head->next;
        while(curr) {
            next = curr->next;
            curr->next = prev;
            prev = curr;
            curr = next;
        }
        return prev;        
    }
    
    bool isPalindrome(ListNode* head, ListNode* head2) {
        ListNode *curr = head, *curr2 = head2;
        while(curr && curr2) {
            if(curr->val != curr2->val) return false;
            curr = curr->next;
            curr2 = curr2->next;
        }
        return true;
    }
};
```
