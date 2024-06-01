---
title: 【LeetCode】141. Linked List Cycle 解題報告
categories:
  - LeetCode
date: 2022-06-19 21:30:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/vLfkbbY.jpg
---

# 141. Linked List Cycle / Easy

Given head, the `head` of a linked list, determine if the linked list has a cycle in it.

There is a cycle in a linked list if there is some node in the list that can be reached again by continuously following the `next` pointer. Internally, pos is used to denote the index of the node that tail's next pointer is connected to. Note that pos is not passed as a parameter.

Return `true` if there is a cycle in the linked list. Otherwise, return `false`.

<!-- more -->

## Example 1:
![](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist.png)
> Input: head = [3,2,0,-4], pos = 1
> IOutput: true
> IExplanation: There is a cycle in the linked list, where the tail connects to the 1st node (0-indexed).

## Example 2:
![](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist_test2.png)
> Input: head = [1,2], pos = 0
> Output: true
> Explanation: There is a cycle in the linked list, where the tail connects to the 0th node.
 
## Example 3:
![](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist_test3.png)
> Input: head = [1], pos = -1
> Output: false
> Explanation: There is no cycle in the linked list.

## Constraints:
> - The number of the nodes in the list is in the range [0, 10^4].
> - -10^5 <= Node.val <= 10^5
> - pos is -1 or a valid index in the linked-list.

# Solution: Floyd's Cycle Finding Algorithm
## 思路

只要碰到在 Linked List 中判斷 Cycle 的題目，就少不了龜兔賽跑演算法（Floyd's Cycle Finding Algorithm）
概念非常簡單，即有兩個指標，一個快一個慢。快指標一次走兩步，慢指標一次一步。
如果快指標走到底，代表沒有迴圈，但若快指標走到最後又和慢指標相會，代表有迴圈存在。

## 效能
### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 4 ms
- Memory Usage: 8 MB
- https://leetcode.com/submissions/detail/616898809/

## Code 
```cpp
class Solution {
public:
    bool hasCycle(ListNode *head) {
        ListNode *fast = head, *slow = head;
        while(fast && fast->next) {
            fast = fast->next->next;
            slow = slow->next;
            if(fast == slow) return true;
        }
        return false;
    }
};
```
