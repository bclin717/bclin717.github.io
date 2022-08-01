---
title: 【LeetCode】362. Design Hit Counter 解題報告
categories:
  - LeetCode
date: 2021-08-01 12:12:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/NhisA2f.jpg
---
 
# 362. Design Hit Counter / Medium
Design a hit counter which counts the number of hits received in the past 5 minutes (i.e., the past 300 seconds).

Your system should accept a timestamp parameter (in seconds granularity), and you may assume that calls are being made to the system in chronological order (i.e., timestamp is monotonically increasing). Several hits may arrive roughly at the same time.

Implement the HitCounter class:

- HitCounter() Initializes the object of the hit counter system.
- void hit(int timestamp) Records a hit that happened at timestamp (in seconds). Several hits may happen at the same timestamp.
- int getHits(int timestamp) Returns the number of hits in the past 5 minutes from timestamp (i.e., the past 300 seconds).

<!-- more --> 
 

## Example 1:
> Input
> ["HitCounter", "hit", "hit", "hit", "getHits", "hit", "getHits", "getHits"]
> [[], [1], [2], [3], [4], [300], [300], [301]]
> Output
> [null, null, null, null, 3, null, 4, 3]

> Explanation
> HitCounter hitCounter = new HitCounter();
> hitCounter.hit(1);       // hit at timestamp 1.
> hitCounter.hit(2);       // hit at timestamp 2.
> hitCounter.hit(3);       // hit at timestamp 3.
> hitCounter.getHits(4);   // get hits at timestamp 4, return 3.
> hitCounter.hit(300);     // hit at timestamp 300.
> hitCounter.getHits(300); // get hits at timestamp 300, return 4.
> hitCounter.getHits(301); // get hits at timestamp 301, return 3.

## Constraints: 
> - 1 <= timestamp <= 2 * 10^9
> - All the calls are being made to the system in chronological order (i.e., timestamp is monotonically increasing).
> - At most 300 calls will be made to hit and getHits.

# Solution: Queue
## 思路 

這題要我們設計一個 class，當呼叫 hit(ts) 時，會記錄 timestamp，呼叫 getHits(ts) 時，要回傳此 timestamp 前 300 秒有多少 hit。
我們可以利用 queue 來儲存，由於題目保證給定的 timestamp 只會相等或增加，我們設計的 queue 會儲存一個 pair，first 存 timestamp，second 存次數。
每次 hit 時我們只關注是否要 push 或是將最後一個 timestamp 的次數往上增加，並且用一個變數 total 來儲存有幾個 hit。
getHits 時，我們計算 queue 結尾的 timestamp 與開頭的 timestamp 差異是否超過 300，如果是，就不斷 pop 直到 queue 中只有這 300 秒的 hits。
每次 pop 都要從 total 扣掉次數，最後回傳 total 即可。

時間複雜度在最佳的狀況下，每次操作都是 O(1)，最差狀況下，每次都要把 queue 裡的 pop 掉，用到 O(N)
而空間複雜度則因為最多保證是 300 個 pair，所以是常數空間。


## 效能

### Complexity 
- Time Complexity: 
  - hit() - O(1)
  - getHits() - O(1), worst O(N)
- Space Complexity: O(1)

### LeetCode Result

- Runtime: 0 ms
- Memory Usage: 7.4 MB 
- https://leetcode.com/submissions/detail/761364251/

## Code
```cpp
class HitCounter {
public:
    HitCounter() {}
    
    void hit(int timestamp) {
        if(q.empty() || q.back().first != timestamp) {
            q.push({timestamp, 1});
        } else {
            q.back().second++;
        }
        ++total;
    }
    
    int getHits(int timestamp) {
        while(!q.empty() && timestamp - q.front().first >= 300) {
            total -= q.front().second;
            q.pop();
        }
        return total;
    }
private:
    queue<pair<int, int>> q;
    int total = 0;
    
};
```