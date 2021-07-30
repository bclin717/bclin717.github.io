---
title: 【LeetCode】19. Remove Nth Node From End of List 解題報告
categories:
  - LeetCode
date: 2021-07-30 22:02:56
tags: LeetCode
photos:
    - https://i.imgur.com/XUq1eEE.jpg
---
 
# 19. Remove Nth Node From End of List / Medium

Given the head of a linked list, remove the nth node from the end of the list and return its head.

<!-- more --> 
![](https://assets.leetcode.com/uploads/2020/10/03/remove_ex1.jpg)

## Example 1:
> Input: head = [1,2,3,4,5], n = 2
> Output: [1,2,3,5]

## Example 2:
> Input: head = [1], n = 1
> Output: []

## Example 3:
> Input: head = [1,2], n = 1
> Output: [1]


# Solution 1: 2 Pass
## 思路

其實是很直覺的想法，先算出整個 List 有多長，減去 n 就是我們要消除的 index 了。都是很簡單的 Linked-List 操作。
只是如果我們要消除的第 n 個 node 其實是 head 就會麻煩一點，必須單獨一個 if-condition，為了簡化操作，
我們在 head 前多宣告一個 dummy head node，並將 dummy node 作為 head 進行操作，最後只要回傳 dummy 的下一個節點即可。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 0 ms
- Memory Usage: 10.7 MB 
- https://leetcode.com/submissions/detail/530506512/

## Code 
```cpp
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        if(head->next == nullptr) return nullptr;
        int size = 0;
        ListNode* cur = head;
        ListNode dummy(-1, head);
        
        while(cur != nullptr) {
            ++size;
            cur = cur->next;
        }
        size -= n;
        cur = &dummy;
        while(size--) cur = cur->next;
        cur->next = cur->next->next;
        return dummy.next;  
    }
};
```


# Solution 2: 1 Pass
## 思路

前一個解法需要兩次在 List 上做搜尋和操作，第一次要 n 次，最差的狀況 n = 1，第二次搜尋也要 n-1 次，嚴格來說時間複雜度是 2N。
那我們能不能只遍歷一次 List 就好？

可以！

因為要尋找的是倒數第 n 個，也就是說倒數第 n 個 node index 跟最後的 index 相差了 n-1，
如果我們能有兩個指標 - 快慢指標，兩者 index 剛好相差為 n，這樣一來快指標跑到最後一個 node 的時候，慢指標剛好會落在我們要消除的 node 的前一個。
於是，我們便能輕易地刪除掉倒數第 n 個 node，最多遍歷 n 次。
但可能因為 LeetCode 的測資太小，實際上和第一種解法並沒有速度上的優勢，甚至還慢一點（應該是運行誤差）。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 4 ms
- Memory Usage: 10.8 MB 
- https://leetcode.com/submissions/detail/530623344/

## Code 
```cpp
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        if(head->next == nullptr) return nullptr;
        ListNode *fast, *slow, dummy(-1, head);
        dummy.next = head;
        fast = &dummy; 
        slow = &dummy;
        while(n--) {
            fast = fast->next;
        }
        while(fast->next != nullptr) {
            fast = fast->next;
            slow = slow->next;
        }
        slow->next = slow->next->next;
        return dummy.next;
    }
};
```