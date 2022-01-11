---
title: 【LeetCode】21. Merge Two Sorted Lists 解題報告
categories:
  - LeetCode
date: 2021-08-02 20:53:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/J8AhFop.jpg
---
 
# 21. Merge Two Sorted Lists / Easy

Merge two sorted linked lists and return it as a sorted list. The list should be made by splicing together the nodes of the first two lists.

<!-- more --> 

## Example 1:
![](https://assets.leetcode.com/uploads/2020/10/03/merge_ex1.jpg)
> Input: l1 = [1,2,4], l2 = [1,3,4]
> Output: [1,1,2,3,4,4]

## Example 2:
> Input: l1 = [], l2 = []
> Output: []

## Example 3:
> Input: l1 = [], l2 = [0]
> Output: [0]

## Comstraints
> - The number of nodes in both lists is in the range [0, 50].
> - -100 <= Node.val <= 100
> - Both l1 and l2 are sorted in non-decreasing order.

# Solution 1: Vector
## 思路
很簡單，就是把兩個 List 的元素都放進 vector，排序後再接一條新的 List。

## 效能

### Complexity 
- Time Complexity: O((N+M)log(N+M))
- Space Complexity: O(N+M)

### LeetCode Result

- Runtime: 9 ms
- Memory Usage: 15.1 MB 
- https://leetcode.com/submissions/detail/531337188/

## Code
```cpp
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        if(l1 == nullptr && l2 == nullptr) return nullptr;
        if(l1 == nullptr) return l2;
        if(l2 == nullptr) return l1;
        vector<int> v;
        ListNode* cur = l1;
        while(cur != nullptr) {
            v.emplace_back(cur->val);
            cur = cur->next;
        }
        cur = l2;
        while(cur != nullptr) {
            v.emplace_back(cur->val);
            cur = cur->next;
        }
        sort(v.begin(), v.end());
        
        ListNode dummy(0);
        cur = &dummy;
        for(int num: v) {
            cur->next = new ListNode(num);
            cur = cur->next;
        }
        return dummy.next;

    }
};
```

# Solution 2: LL Operation
## 思路

建一個新的頭，每次都進行比較，下一個就接上比較小的那一邊。

## 效能

### Complexity 
- Time Complexity: O(N+M)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 4 ms
- Memory Usage: 14.8 MB 
- https://leetcode.com/submissions/detail/532087713/

## Code
```cpp 
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        if(!l1) return l2;
        else if(!l2) return l1;
        
        ListNode* dummy = new ListNode(0);
        ListNode* head = dummy;
        
        while(l1 && l2) {
            if(l1->val <= l2->val) {
                dummy->next = l1;
                l1 = l1->next;
                dummy = dummy->next;
                dummy->next = l2;
            } else {
                dummy->next = l2;
                l2 = l2->next;
                dummy = dummy->next;
                dummy->next = l1;
            }
        }
        return head->next;
    }
};
```


# Solution 3: Recursion
## 思路

很簡潔的遞迴寫法。由於最後回傳的節點是由小到大，也就是說 head 是最小的，
我們可以比較出比較小的節點作為頭，接著把下一個節點當作 head 和另一條 List 再合併一次，這次也會回傳比較小的那邊。
最後就連起來了。

## 效能

### Complexity 
- Time Complexity: O(N+M)
- Space Complexity: O(N+M)

### LeetCode Result

- Runtime: 4 ms
- Memory Usage: 14.8 MB 
- https://leetcode.com/submissions/detail/532086451/

## Code
```cpp
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        if(!l1) return l2;
        else if(!l2) return l1;
        
        if(l1->val < l2->val) {
            l1->next = mergeTwoLists(l1->next, l2);
            return l1;
        } else {
            l2->next = mergeTwoLists(l1, l2->next);
            return l2;
        }
    }
};
```
