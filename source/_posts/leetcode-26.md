---
title: 【LeetCode】26. Remove Duplicates from Sorted Array 解題報告
categories:
  - LeetCode
date: 2021-08-20 22:00:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/EQmHEnm.jpg
---
 
# 26. Remove Duplicates from Sorted Array / Easy

Given an integer array nums sorted in non-decreasing order, remove the duplicates in-place such that each unique element appears only once. The relative order of the elements should be kept the same.
<!-- more --> 

Since it is impossible to change the length of the array in some languages, you must instead have the result be placed in the first part of the array nums. More formally, if there are k elements after removing the duplicates, then the first k elements of nums should hold the final result. It does not matter what you leave beyond the first k elements.

Return k after placing the final result in the first k slots of nums.

Do not allocate extra space for another array. You must do this by modifying the input array in-place with O(1) extra memory.

### Custom Judge:

The judge will test your solution with the following code:

> int[] nums = [...]; // Input array
> int[] expectedNums = [...]; // The expected answer with correct length

> int k = removeDuplicates(nums); // Calls your implementation

> assert k == expectedNums.length;
> for (int i = 0; i < k; i++) {
>     assert nums[i] == expectedNums[i];
> }
> If all assertions pass, then your solution will be accepted.



## Example 1:
> Input: nums = [1,1,2]
> Output: 2, nums = [1,2,_]
> Explanation: Your function should return k = 2, with the first two elements of nums being 1 and 2 respectively.
> It does not matter what you leave beyond the returned k (hence they are underscores).

## Example 2:
> Input: nums = [0,0,1,1,1,2,2,3,3,4]
> Output: 5, nums = [0,1,2,3,4,_,_,_,_,_]
> Explanation: Your function should return k = 5, with the first five elements of nums being 0, 1, 2, 3, and 4 respectively.
> It does not matter what you leave beyond the returned k (hence they are underscores).

## Constraints:
> 0 <= nums.length <= 3 * 10^4
> -100 <= nums[i] <= 100
> nums is sorted in non-decreasing order.


# Solution: 
## 思路


因為 array 是排序過的，我們可以用一個指標 i 先往後移動，掠過前後相同的元素，尋找不同的元素。
另一個指標則指在前方，理想上 index 0 應該放最小的，index 1 則是次小的，找到不同的元素時，就放到前面的指標。變相把不重複的元素放在最前面。


## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 7 ms
- Memory Usage: 18.4 MB 
- https://leetcode.com/submissions/detail/538368359/

## Code
```cpp
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        int n = nums.size();
        if(0 == n) return 0;

        int cnt = 1;
        for(int i = 0; i < n-1; ++i) {
            if(nums[i] != nums[i+1]) {
                nums[cnt] = nums[i+1];
                ++cnt;
            }
        }
        return cnt;
    }
};
```