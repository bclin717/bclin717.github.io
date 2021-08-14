---
title: 【LeetCode】146. LRU Cache 解題報告
categories:
  - LeetCode
date: 2021-08-14 20:12:56
tags: LeetCode
photos:
    - https://i.imgur.com/0N5wjs4.jpg
---
 
# 146. LRU Cache / Medium

Design a data structure that follows the constraints of a Least Recently Used (LRU) cache.

Implement the LRUCache class:

- LRUCache(int capacity) Initialize the LRU cache with positive size capacity.
- int get(int key) Return the value of the key if the key exists, otherwise return -1.
- void put(int key, int value) Update the value of the key if the key exists. Otherwise, add the key-value pair to the cache. If the number of keys exceeds the capacity from this operation, evict the least recently used key.


The functions get and put must each run in O(1) average time complexity.

<!-- more --> 


## Example 1:
> ### Input
> ["LRUCache", "put", "put", "get", "put", "get", "put", "get", "get", "get"] \
> [[2], [1, 1], [2, 2], [1], [3, 3], [2], [4, 4], [1], [3], [4]]
> ### Output
> [null, null, null, 1, null, -1, null, -1, 3, 4]

> ### Explanation
> LRUCache lRUCache = new LRUCache(2);
> lRUCache.put(1, 1); // cache is {1=1}
> lRUCache.put(2, 2); // cache is {1=1, 2=2}
> lRUCache.get(1);    // return 1
> lRUCache.put(3, 3); // LRU key was 2, evicts key 2, cache is {1=1, 3=3}
> lRUCache.get(2);    // returns -1 (not found)
> lRUCache.put(4, 4); // LRU key was 1, evicts key 1, cache is {4=4, 3=3}
> lRUCache.get(1);    // return -1 (not found)
> lRUCache.get(3);    // return 3
> lRUCache.get(4);    // return 4

## Constraints:
> - 1 <= capacity <= 3000
> - 0 <= key <= 104
> - 0 <= value <= 105
> - At most 2 * 105 calls will be made to get and put.


# Solution: DP
## 思路
照著題目定義實作即可。

要注意的是，Unordered_map 的 key 存 int，value 則是存 list 的 iterator，方便直接操作 list。list 中存了一個 pair<key, value>，這樣也能從 list 直接反查 map，效率會好很多。

在將 list 的某一個 node 移動到 list 的開頭時，這邊是利用 list.splice() 這個 function。

## 效能

### Complexity 
- Time Complexity:
  - get(): O(N)
  - put(): O(N) 
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 376 ms
- Memory Usage: 165 MB 
- https://leetcode.com/submissions/detail/538139303/

## Code
```cpp
class LRUCache{     
public:
    LRUCache(size_t capacity) : _capacity(capacity) { }
    
    int get(int key) {
        if(const auto&& it = hmap.find(key); it != hmap.end()) {
            dlist.splice(dlist.begin(), dlist, it->second);
            return it->second->second;
        }
        return -1;
    }
    
    void put(int key, int value) {
        if(const auto&& it = hmap.find(key); it != hmap.end()) {
            dlist.splice(dlist.begin(), dlist, it->second);
            it->second->second = value;
            return;
        } 
        
        if(dlist.size() == _capacity) {
            hmap.erase(dlist.back().first);
            dlist.pop_back();
        }
        hmap[key] = dlist.insert(dlist.begin(), {key, value});
    }
private:
    unordered_map<int, list<pair<int, int>>::iterator> hmap;
    list<pair<int, int>> dlist;
    int _capacity = 0;
};
```