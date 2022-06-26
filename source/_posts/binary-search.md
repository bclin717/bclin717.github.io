---
title: Binary Search 二分搜尋法
categories:
  - Algorithm
date: 2022-06-26 21:23:00
hidden: false
photos:
    - https://i.imgur.com/IvaIl1H.jpg
---

Binary Search 大概是很多人學習演算法的入門題（或是 Bubble Sort？），Binary Search 的觀念很簡單，透過每次減半搜尋空間來達到 O(logN) 的時間複雜度，但其細節和變形的用法確非常多。
實作上只要一個邊界設錯，結果就會大不同。刷題的時候總是讓人覺得很煩燥。但只要題目要求搜尋要比 O(N) 快，幾乎是肯定一定要實作 Binary Search 了。

# 基本用法

最基礎的 Binary Search 用法，程式碼如下。
如果有找到則回傳 index，沒有的話回傳 -1（回傳 left 則是最接近的位置）。

## 參考題目：[LeetCode - 35. Search Insert Position](https://leetcode.com/problems/search-insert-position/)

```cpp
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        int l = 0, r = nums.size()-1;
        while (l <= r) {
            int mid = l + (r-l)/2;
            if(nums[mid] > target) {
                r = mid-1;
            } else if(nums[mid] < target) {
                l = mid+1;
            } else {
                return mid;
            }
        }
        return l;
    }
};
```

## 參考題目：[LeetCode - 278. First Bad Version](https://leetcode.com/problems/first-bad-version/)

```cpp
class Solution {
public:
    int firstBadVersion(int n) {
        unsigned int l = 1, r = n;
        while(l < r) {
            int mid = l + (r-l)/2;
            if(isBadVersion(mid)) {
                r = mid;
            } else {
                l = mid+1;
            }
        }
        return l;
    }
};
```

## mid 的算法

或許你會好奇為什麼計算中點的部分，要使用
```cpp
int mid = l + (r-l)/2;
```
而不是使用下面比較簡潔的寫法
```cpp
int mid = (r+l)/2;
```

這是因為，如果 r+l 有可能會超過他們本來資料型態的最大值。
若 r 與 l 都是 int，那 r+l 很有可能超出 ```2^31–1```


# 有序但值重複

有一種情況是，雖然陣列有排序，但值是重複的。這種情況通常會需要找出
1. 找出「大於或等於」某值的「最小值」的位置 - lower_bound() in C++
2. 找出「大於」某值的「最小值」的位置 - upper_bound() in C++

## 參考題目：[LeetCode - 34. Find First and Last Position of Element in Sorted Array](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

這一題其實是變相要你設計 C++ 中的 lower_bound 與 upper_bound（儘管需要減 1）。
至於為何搜尋右邊界時，需要將 target+1 再將結果 -1，我們可以用範例來思考。
例如，範例 [5,7,7,8,8,10] 中，如果我們尋找 9，會得到 10 的位置，這時候再減去 1，就得到 8 的右邊界位置了。

```cpp
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        if(!binary_search(nums.begin(), nums.end(), target)) return {-1, -1}; 
        int l = lower_bound(nums, target);
        int r = lower_bound(nums, target+1)-1;
        return {l, r};
    }
    
    int lower_bound(vector<int>& nums, int target) {
        int l = 0, r = nums.size()-1;
        while(l <= r) {
            int mid = l + (r-l)/2;
            if(nums[mid] >= target) {
                r = mid-1;
            } else {
                l = mid+1;
            }
        }
        return l;
    }
};
```

# 非排序

有時候會遇到題目搜尋一個特別設計過的陣列，這個陣列雖然沒有排序過，但顯然與題目存在某種關係。

## 參考題目：[LeetCode - 162. Find Peak Element](https://leetcode.com/problems/find-peak-element/)

題目設計了一個陣列是部分排序的，只要回傳任一高點即可。
以前面提過的寫法，如果條件設成 `l <= r` 會多出很多邊界條件要處理，因為這一題需要判斷兩個點位的關係。

### 寫法一
```cpp
class Solution {
public:
    int findPeakElement(vector<int>& nums) {
        int l = 0, r = nums.size()-1;
        while(l < r) {
            int mid = l + (r - l)/2;
            if(nums[mid] < nums[mid+1]) {
                l = mid+1;
            } else {
                r = mid;
            }
        }
        return l;
    }
};
```

### 寫法二
```cpp
class Solution {
public:
    int findPeakElement(vector<int>& nums) {
        if(nums.size() == 1) return 0;
        int l = 1, r = nums.size()-2, n = nums.size();
        
        if(nums[0] > nums[1]) return 0;
        if(nums[n-1] > nums[n-2]) return n-1;
        
        while(l <= r) {
            int mid = l+(r-l)/2;
            if(nums[mid] < nums[mid+1]) {
                l = mid+1;
            } else {
                r = mid-1;
            }
        }
        return l;
    }
};
```


## 參考題目：[LeetCode - 33. Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/)

這題與前一題有點類似，也是特別設計過，沒有排序但部分排序的題目。
和前一題一樣，按照題目的要求去切每一種狀況做判斷。

```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0, right = nums.size() - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) 
                return mid;
            if (nums[mid] >= nums[left]) {
                if (nums[left] <= target && nums[mid] > target) 
                    right = mid - 1;
                else 
                    left = mid + 1;
            } else {
                if (nums[mid] < target && nums[right] >= target) 
                    left = mid + 1;
                else 
                    right = mid - 1;
            }
        }
        return -1;
    }
};
```