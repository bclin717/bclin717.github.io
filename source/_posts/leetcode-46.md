---
title: 【LeetCode】46. Permutations 解題報告
categories:
  - LeetCode
date: 2022-06-25 17:10:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/ZagOLlK.jpg
---
 
# 46. Permutations / Medium

Given an array `nums` of distinct integers, return all the possible permutations. You can return the answer in **any order**.


<!-- more --> 
 

## Example 1:
> Input: nums = [1,2,3]
> Output: [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]

## Example 2:
> Input: nums = [0,1]
> Output: [[0,1],[1,0]]

## Example 3:
> Input: nums = [1]
> Output: [[1]]

## Constraints: 
> - 1 <= nums.length <= 6
> - -10 <= nums[i] <= 10
> - All  the integers of nums are unique.

# Solution 1: C++ implementation of next_permutation
## 思路 

C++ 內建一個函式叫 next_permutation，每次呼叫都會給回傳給定陣列的下一個排列。
面試時應該是無法使用這個函式，而 C++ 的 next_permutataion 實作過程如下動畫。

![](https://helloacm.com/wp-content/uploads/2019/09/6494F03B-2089-487C-90BD-44BDD78ACBB1.gif)

時間複雜度是 O(N・N!)，因為排列有 N! 種，每次都必須用 O(N) 去找出下一個排列，所以總共是 O(N・N!)次。
最好的狀態是陣列中全都是相同的值，最壞的狀態是每一個值都不同。

## 效能

### Complexity 
- Time Complexity: O(N ・ N!), where N is the length of nums.
- Space Complexity: O(1), not containing return value.

### LeetCode Result

- Runtime: 0 ms
- Memory Usage: 7.5 MB 
- https://leetcode.com/submissions/detail/541337982/

## Code
```cpp
class Solution {
public:
    vector<vector<int>> permute(vector<int>& nums) {
        vector<vector<int>> permutations;
        sort(nums.begin(), nums.end());
        do {
            permutations.emplace_back(nums);
        } while(NextPermutation(nums));
        return permutations;
    }
    
    bool NextPermutation(vector<int>& nums) {
        if(nums.size() <= 1) return false;
        int i = nums.size()-2;
        while((i >= 0) && (nums[i] >= nums[i+1])) --i;
        if(i < 0) return false;
        if(i >= 0) {
            int j = nums.size()-1;
            while((j >= i) && (nums[j] <= nums[i])) --j;
            swap(nums[i], nums[j]);
        }
        reverse(nums.begin()+i+1, nums.end());
        return true;
    }
};
```

# Solution 2: Bactracking
## 思路 

比較直覺也比較容易實作的方式是用 Backtracking。
每次都將目前標記的位置（從第一個開始，程式中的 first），然後依序和後面所有的值都交換一次，接著進入下一個狀態。最 first+1 的位置開始往後做交換。

![](https://i.imgur.com/iOmayLB.png)

## 效能

### Complexity 
- Time Complexity: Better than O(N・N!) and Slower than O(N!). [This is so-called k-permutations_of_n, or partial permutation.](https://en.wikipedia.org/wiki/Permutation#k-permutations_of_n)
- Space Complexity: O(1)


### LeetCode Result

- Runtime: 4 ms
- Memory Usage: 7.6 MB 
- https://leetcode.com/submissions/detail/730601040/

## Code
```cpp
class Solution {
public:
    vector<vector<int>> permute(vector<int>& nums) {
        permutate(nums, 0);
        return permutations;
    }
    
    void permutate(vector<int>& nums, int first){
        if(nums.size() == first) {
            permutations.emplace_back(nums);
            return;
        }
        
        for(int i = first; i < nums.size(); ++i) {
            swap(nums[i], nums[first]);
            permutate(nums, first+1);
            swap(nums[i], nums[first]);
        }
    }
    
private:
    vector<vector<int>> permutations;
};
```