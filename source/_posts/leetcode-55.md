---
title: 【LeetCode】55. Jump Game 解題報告
categories:
  - LeetCode
date: 2021-08-07 20:02:56
tags: LeetCode
photos:
    - https://i.imgur.com/tiVoidl.jpg
---
 
# 55. Jump Game / Medium

You are given an integer array nums. You are initially positioned at the array's first index, and each element in the array represents your maximum jump length at that position.

Return true if you can reach the last index, or false otherwise.

 

<!-- more --> 
## Example 1:
> Input: nums = [2,3,1,1,4]
> Output: true
> Explanation: Jump 1 step from index 0 to 1, then 3 steps to the last index.

## Example 2:
> Input: nums = [3,2,1,0,4]
> Output: false
> Explanation: You will always arrive at index 3 no matter what. Its maximum jump > length is 0, which makes it impossible to reach the last index.


## Constraints:
> - 1 <= nums.length <= 104
> - 0 <= nums[i] <= 105
 


# Solution 1: DP (Top-Down)
## 思路

dp[i] 代表是否能到達第i個位置，從 index 0，也就是起點開始，每一個位置都更新一輪 dp[]。最差時間複雜度 O(N^2) ，效能很差，但很直覺。


## 效能

### Complexity 
- Time Complexity: O(N^2)
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 1744 ms
- Memory Usage: 48.2 MB 
- https://leetcode.com/submissions/detail/534494162/

## Code
```cpp
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int n = nums.size();
        bool dp[n];
        for(int i = 0; i < n; ++i) {
            dp[i] = false;
        }
        dp[0] = true;
        
        for(int i = 0; i < n-1; ++i) {
            if(dp[i]) {
                for(int j = i+1; j <= nums[i]+i && j < n; ++j) {
                    dp[j] = true;
                }
            }
        }
        return dp[n-1];
    }
};
```

# Solution 2: DP (Bottom-Up)
## 思路

但 Solution 1 相反，我們想知道這顆決策樹是否能從抵達終點的葉節點往回推，推回到起點。
所以改成從終點出發，如果有節點 index 的位置加上能跳躍的距離（index + nums[index]）大於終點的位置，就可以把問題從「是否能抵達終點(size-1)」reduced 成「是否能抵達 index」，藉由這樣的策略，只需要 O(N)，不需要雙迴圈。


## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(N)

### LeetCode Result

- Runtime: 60 ms
- Memory Usage: 48.6 MB 
- https://leetcode.com/submissions/detail/534543318/

## Code
```cpp
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int n = nums.size();
        vector<bool> dp(n, false);
        int i = n-1, j = n-1;
        dp[n-1] = true;
        while(i >= 0 && j >= 0) {
            if(i + nums[i] >= j) {
                dp[i] = true;
                j = i;
            }
            --i;
        }
        return dp[0];
    }
};
```

# Solution 3: Linear Check
## 思路

從 Solution 2 再思考，其實不需要用到 dp 陣列，dp 是為了讓我們使用已解決之子問題的答案，來解決目前的問題。但其實我們只要不斷 reduced 問題，更新最右能抵達的節點 (last)，最後判斷這個節點是不是起點(0)即可。


## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 48 ms
- Memory Usage: 48.4 MB 
- https://leetcode.com/submissions/detail/534548588/

## Code
```cpp
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int last = nums.size() - 1;
        for(int i = last; i >= 0; --i) {
            if(nums[i] + i >= last) {
                last = i;
            }
        }
        return last == 0;
    }
};
```

# Solution 4: Greedy
## 思路

但是，最有效率的方式應該是 Greedy。
從起點出發，不斷更新最遠能抵達的距離，如果目前的位置超出這個距離，代表我們已經到了到不了的地方，可以直接 return false；若超出終點的位置，就代表就可以抵達終點了。


## 效能

### Complexity 
- Time Complexity: O(N)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 48 ms
- Memory Usage: 48.2 MB 
- https://leetcode.com/submissions/detail/534548115/

## Code
```cpp
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int n = nums.size(), maxDistance = 0;
        for(int i = 0; i < n; ++i) {
            if(maxDistance < i) return false;
            maxDistance = max(maxDistance, i + nums[i]);
            if(maxDistance >= n-1) return true;
        }
        return false;
    }
};
```