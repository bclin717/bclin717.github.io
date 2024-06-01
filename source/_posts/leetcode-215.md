---
title: 【LeetCode】215. Kth Largest Element in an Array 解題報告
categories:
  - LeetCode
date: 2022-02-12 15:00:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/w6OkXhm.jpg
---
 
# 215. Kth Largest Element in an Array / Medium

Given an integer array nums and an integer k, return the kth largest element in the array.

Note that it is the kth largest element in the sorted order, not the kth distinct element.

<!-- more --> 

## Example 1:
> Input: nums = [3,2,1,5,6,4], k = 2
> Output: 5

## Example 2:
> nums = [3,2,3,1,2,4,5,5,6], k = 4
> Output: 4

## Constraints: 
> - 1 <= k <= nums.length <= 10^4
> - -10^4 <= nums[i] <= 10^4


# Solution 1: Sort
## 思路

要找第 K 大的元素，也就等於是找出陣列由小到大排序後的倒數第 k 個。

## 效能

### Complexity 
- Time Complexity: O(NlogN)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 4 ms
- Memory Usage: 10 MB 
- https://leetcode.com/submissions/detail/540802122/

## Code
```cpp
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        sort(nums.begin(), nums.end());
        return nums[nums.size()-k];
    }
};
```

# Solution 2: Heap
## 思路

利用 Heapselect，把小於第 K 大的元素都丟掉。

## 效能

### Complexity 
- Time Complexity: O(NlogK)
- Space Complexity: O(NlogK)

### LeetCode Result

- Runtime: 8 ms
- Memory Usage: 10 MB 
- https://leetcode.com/submissions/detail/637253039/

## Code
```cpp
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        priority_queue<int, vector<int>, greater<int>> pq;
        for(const auto& num: nums) {
            pq.push(num);
            if(pq.size() > k) pq.pop();
        }
        return pq.top();
    }
};
```

# Solution 3: C++ STL: partial_sort()
## 思路

partial_sort() 底層的實作是 Heapselect，即 Solution 2 的解法。
由於是使用 heap，其保證從 begin 到 begin + k 是排序的，其餘部分則不保證。
在資料集較小的情況下，Heapselect 有速度上的優勢，問題在於 Heapselect 會留下一個 Heap 結構，
若之後不需要用到裏頭的資料，等於浪費了記憶體空間。

## 效能

### Complexity 
- Time Complexity: O(NlogK)
- Space Complexity: O(NlogK)

### LeetCode Result

- Runtime: 8 ms
- Memory Usage: 10 MB 
- https://leetcode.com/submissions/detail/637874876/

## Code
```cpp
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        partial_sort(nums.begin(), nums.begin() + k, nums.end(), greater<int>());
        return nums[k - 1];
    }
};
```

# Solution 4: C++ STL: nth_element()
## 思路

nth_element() 的底層是 Quickselect，他會將第 n 大的元素放在第 n-1 位置，其前後則不保證排序。

## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(1)
- https://leetcode.com/submissions/detail/639693460/

### LeetCode Result

- Runtime: 3 ms
- Memory Usage: 9.9 MB 

## Code
```cpp
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        nth_element(nums.begin(), nums.begin() + k - 1, nums.end(), greater<int>());
        return nums[k - 1];
    }
};
```


# Solution 5: Quickselect / Hoare's selection
## 思路

即 C++ STL 中 nth_element() 使用的演算法，由 Hoare 提出，概念近似於 Quick Sort，在平均狀況下只要 O(n) 的時間複雜度（Reduced from O(NlogN)）

其概念為：選出一個 pivot，將小於 pivot 的元素向左放，大於的向右邊放，也就是將元素大小依 pivot 為標準劃分（Parition）成兩個部分。
並依第 k 個元素是大於或小於 pivot 的 index，對那一部分再進行切割。
若選出的 pivot index 正好是 k，即回傳 pivot 值，即為題目要求的第 k 大的值。


## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(1)
- https://leetcode.com/submissions/detail/637874238/

### LeetCode Result

- Runtime: 0 ms
- Memory Usage: 9.9 MB 

## Code
```cpp
class Solution {
public:
    int partition(int left, int right, int pivot_index, vector<int>& nums) {
        int pivot = nums[pivot_index];
        int stored_index = left;
        
        swap(nums[pivot_index], nums[right]);
        for(int i = left; i <= right; ++i) {
            if(nums[i] < pivot) {
                swap(nums[stored_index], nums[i]);
                ++stored_index;
            }
        }
        swap(nums[stored_index], nums[right]);
        
        return stored_index;
    }
    
    int quickselect(int left, int right, int k_smallest, vector<int>& nums) {
        if(left == right) return nums[left];
        
        int pivot_index = left + rand() % (right - left + 1);
        pivot_index = partition(left, right, pivot_index, nums);
        
        if(pivot_index == k_smallest) return nums[k_smallest];

        else if(k_smallest < pivot_index) {
            return quickselect(left, pivot_index-1, k_smallest, nums);
        } else {
            return quickselect(pivot_index+1, right, k_smallest, nums);
        }
        
    }
    
    int findKthLargest(vector<int>& nums, int k) {
        int n = nums.size();
        return quickselect(0, n-1, n-k, nums);
    }
};
```