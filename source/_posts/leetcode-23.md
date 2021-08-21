---
title: 【LeetCode】23. Merge k Sorted Lists 解題報告
categories:
  - LeetCode
date: 2021-08-03 11:23:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/eNACgsy.jpg
---
 
# 23. Merge k Sorted Lists / Hard

You are given an array of k linked-lists lists, each linked-list is sorted in ascending order.

Merge all the linked-lists into one sorted linked-list and return it.

<!-- more --> 

## Example 1:
> Input: lists = [[1,4,5],[1,3,4],[2,6]]
> Output: [1,1,2,3,4,4,5,6]
> Explanation: The linked-lists are:
> [
>   1->4->5,
>   1->3->4,
>   2->6
> ]
> merging them into one sorted list:
> 1->1->2->3->4->4->5->6


## Example 2:
> Input: lists = []
> Output: []

## Example 3:
> Input: lists = [[]]
> Output: []

## Constaints
> - k == lists.length
> - 0 <= k <= 10^4
> - 0 <= lists[i].length <= 500
> - -10^4 <= lists[i][j] <= 10^4
> - lists[i] is sorted in ascending order.
> - The sum of lists[i].length won't exceed 10^4.

# Solution 1: Merge lists one by one (Recursion)
## 思路
這題的解法很多，最一開始想到的就是把 LeetCode 21 Merge 2 Sorted Lists 的解答包一包丟進去用，雖然是過了，但速度很慢。

## 效能

### Complexity 
- Time Complexity: O(kN) where k is the number of linked lists.
- Space Complexity: O(kN) where k is the number of linked lists.

### LeetCode Result

- Runtime: 232 ms
- Memory Usage: 13.4 MB 
- https://leetcode.com/submissions/detail/532106644/

## Code
```cpp
class Solution {
public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        if(lists.empty()) return nullptr;
        
        ListNode* l1 = nullptr;
        for(const auto& l2: lists) {
            l1 = mergeTwoLists(l1, l2);
        }
        return l1;
    }
    
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


# Solution 2: Merge lists one by one

## 思路
一樣也是把 Merge 2 Sorted Lists 的解法拿來用。

## 效能

### Complexity 
- Time Complexity: O(kN) where k is the number of linked lists.
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 160 ms
- Memory Usage: 13.4 MB 
- https://leetcode.com/submissions/detail/532106865/

## Code
```cpp
class Solution {
public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        if(lists.empty()) return nullptr;
        ListNode* l1 = nullptr;
        for(auto& l2 : lists) {
            if(!l1) {
                l1 = l2;
                continue;
            } else if(!l2) continue;
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
            l1 = head->next;
        }
        return l1;
    }
};
```


# Solution 3: Brute Force
## 思路
很直覺，把所有的 node 丟到 vector，sort 後再創一條新的 list。

## 效能

### Complexity 
- Time Complexity: O(NlogN) where N is the total number of nodes.
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 20 ms
- Memory Usage: 14.1 MB 
- https://leetcode.com/submissions/detail/532418387/

## Code
```cpp
class Solution {
public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        if(lists.empty()) return nullptr;
        vector<int> v;
        for(auto& node : lists) {
            while(node != nullptr) {
                v.emplace_back(node->val);
                node = node->next;
            }
        }
        
        sort(v.begin(), v.end());
        ListNode* head = new ListNode(0);
        ListNode* cur = head;
        for(int i : v) {
            cur->next = new ListNode(i);
            cur = cur->next;
        }
        
        return head->next;
    }
};
```

# Solution 4: Priority Queue
## 思路

Priority Queue 的底層是 Heap Tree，Max Heap Tree 每次 push 都會做 Max Heapify，這個特性使得樹一直都是排序過的。
我們把 List 的元素一個個取出來放進去 Priority Queue，最後就會是已排序的 Queue，這時再把 List 接回來就好了。

## 效能

### Complexity 
- Time Complexity: O(NlogN) where N is the total number of nodes.
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 20 ms
- Memory Usage: 13.9 MB 
- https://leetcode.com/submissions/detail/532104818/

## Code
```cpp
class Solution {
public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        priority_queue<int> pq;
        ListNode *cur;
        for(const auto& node: lists) {
            cur = node;
            while(cur) {
                pq.push(cur->val);
                cur = cur->next;
            }
        }

        ListNode* head = new ListNode();
        cur = nullptr;
        while(!pq.empty()) {
            cur = head->next;
            head->next = new ListNode(pq.top());
            head->next->next = cur;
            pq.pop();
        }
        
        return head->next;
    }
};
```