---
title: 【LeetCode】138. Copy List with Random Pointer 解題報告
categories:
  - LeetCode
date: 2022-06-06 21:41:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/tXt46wT.jpg
---
 
# 138. Copy List with Random Pointer / Medium

A linked list of length n is given such that each node contains an additional random pointer, which could point to any node in the list, or null.

Construct a deep copy of the list. The deep copy should consist of exactly n brand new nodes, where each new node has its value set to the value of its corresponding original node. Both the next and random pointer of the new nodes should point to new nodes in the copied list such that the pointers in the original list and copied list represent the same list state. None of the pointers in the new list should point to nodes in the original list.

For example, if there are two nodes X and Y in the original list, where X.random --> Y, then for the corresponding two nodes x and y in the copied list, x.random --> y.

Return the head of the copied linked list.

The linked list is represented in the input/output as a list of n nodes. Each node is represented as a pair of [val, random_index] where:

- val: an integer representing Node.val
- random_index: the index of the node (range from 0 to n-1) that the random pointer points to, or null if it does not point to any node.

Your code will only be given the head of the original linked list.

<!-- more --> 

## Example 1:
![](https://assets.leetcode.com/uploads/2019/12/18/e1.png)
> Input: head = [[7,null],[13,0],[11,4],[10,2],[1,0]]
> Output: [[7,null],[13,0],[11,4],[10,2],[1,0]]

## Example 2:
![](https://assets.leetcode.com/uploads/2019/12/18/e2.png)
> Input: head = [[1,1],[2,1]]
> Output: [[1,1],[2,1]]

## Example 3:
![](https://assets.leetcode.com/uploads/2019/12/18/e3.png)
> Input: head = [[3,null],[3,0],[3,null]]
> Output: [[3,null],[3,0],[3,null]]

## Constraints:
> - 0 <= n <= 1000
> - -10^4 <= Node.val <= 10^4
> - Node.random is null or is pointing to some node in the linked list.

# Solution 1: Iterative with O(1) Space
## 思路

這題算是很有趣的一題，因為有了 random pointer 的存在，再也無法一路 next 到 nullptr 為止，有可能會在中間就遇到 nullptr。
Solution 給出一個很有趣的做法，就是將新的 Node 接在舊的 Node 後面，這時新的 random pointer 指的就會是舊的 random pointer 的下一個。
這個過程稱之為 Weaving（編織），最後再將其 Unweaving（解開）即可

![](https://leetcode.com/problems/copy-list-with-random-pointer/Figures/138/138_Copy_List_Random_8_1.png)

先在原來的 Node 後面建立一個新的節點，接下來將其 random 接到舊的 random 的 next

![](https://leetcode.com/problems/copy-list-with-random-pointer/Figures/138/138_Copy_List_Random_9_1.png)

記得中途要處理 random 或是 next 是 nullptr 的情況。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 7 ms
- Memory Usage: 11.3 MB 
- https://leetcode.com/submissions/detail/547269023/

## Code 
```cpp
class Solution {
public:
    Node* copyRandomList(Node* head) {
        if(!head) return nullptr;
        
        Node* ptr = head;
        while(ptr) {
            Node* newNode = new Node(ptr->val);
            newNode->next = ptr->next;
            ptr->next = newNode;
            ptr = newNode->next;
        }
        ptr = head;
        while(ptr) {
            ptr->next->random = ptr->random != nullptr ? ptr->random->next : nullptr;
            ptr = ptr->next->next;
        }
        
        Node* ptr_old_list = head;
        Node* ptr_new_list = head->next;
        Node* head_new = head->next;
        while(ptr_old_list) {
            ptr_old_list->next = ptr_old_list->next->next;
            ptr_new_list->next = ptr_new_list->next != nullptr ? ptr_new_list->next->next : nullptr;
            
            ptr_old_list = ptr_old_list->next;
            ptr_new_list = ptr_new_list->next;
        }
        return head_new;
    }

};
```

# Solution 2: Map
## 思路

我在討論區看到一個概念簡單效能又快的做法，就是利用 Map 或是 HashMap 來儲存新舊 Node 的對應。
因為這題的節點數量最多只有一千，unordered_map(HashMap) 未必會比 map 快，
但以時間複雜度的角度來說，讀取任一資料時 unordered_map 應該要比較快，map 是 O(logN)，unordered_map 則是 O(1)

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 3 ms
- Memory Usage: 11.5 MB 
- https://leetcode.com/submissions/detail/715686702/

## Code 
```cpp
class Solution {
public:
    Node* copyRandomList(Node* head) {
        if(!head) return nullptr;
        map<Node*, Node*> hmap;
        Node *cur = head;
        while(cur) {
            hmap[cur] = new Node(cur->val);
            cur = cur->next;
        }
        
        cur = head;
        while(cur) {
            hmap[cur]->next = hmap[cur->next];
            hmap[cur]->random = hmap[cur->random];
            cur = cur->next;
        }
        return hmap[head];
    }
};
```