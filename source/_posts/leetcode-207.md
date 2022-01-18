---
title: 【LeetCode】207. Course Schedule 解題報告
categories:
  - LeetCode
date: 2022-01-18 14:56:56
tags: LeetCode
hidden: true

---
 
# 207. Course Schedule / Medium

There are a total of numCourses courses you have to take, labeled from 0 to numCourses - 1. You are given an array prerequisites where prerequisites[i] = [ai, bi] indicates that you **must** take course bi first if you want to take course ai.

- For example, the pair [0, 1], indicates that to take course 0 you have to first take course 1.

Return true if you can finish all courses. Otherwise, return false.

<!-- more --> 

## Example 1:
> Input: numCourses = 2, prerequisites = [[1,0]]
> Output: true
> Explanation: There are a total of 2 courses to take. 
> To take course 1 you should have finished course 0. So it is possible.

## Example 2:
> Input: numCourses = 2, prerequisites = [[1,0],[0,1]]
> Output: false
> Explanation: There are a total of 2 courses to take. 
> To take course 1 you should have finished course 0, and to take course 0 you should also have finished course 1. So it is impossible.


## Constraints: 
> - 1 <= numCourses <= 10^5
> - 0 <= prerequisites.length <= 5000
> - prerequisites[i].length == 2
> - 0 <= ai, bi < numCourses
> - All the pairs prerequisites[i] are unique.

# Solution 1: Backtracking / DFS
## 思路

很直覺的作法，把經過的地方記下來，遞迴呼叫，以 graph 的方式思考的話，就是用 DFS 的方式檢查。
若路徑如果重複就是有迴圈。

缺點是，路徑上的點每一次都要重複檢查，很浪費時間。

時間複雜度的部分，一開始需要建圖，這會用掉 O(E) 的時間，E 是不同課程間關係的數量。
最差的狀況，整張圖是一個迴圈，我們需要走 V 次才會碰到重複，V 是課程的數量，
每次都要碰到原來的課程才會停止，總共要走 V^2 次，時間複雜度為 O(V^2)。

## 效能

### Complexity 
- Time Complexity: O(|E|+|V|^2), where |V| is the number of courses, and |E| is the number of dependencies.
- Space Complexity: O(|E|+|V|), with the same denotation as in the above time complexity.

### LeetCode Result
- Runtime: TLE
- Memory Usage: TLE
- https://leetcode.com/submissions/detail/621543086/

## Code
```cpp
class Solution {
public:
    bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
        vector<vector<int>> courses(numCourses, vector<int>());
        vector<bool> path(numCourses, false);
        
        for(auto& prerequisit: prerequisites) {
            courses[prerequisit[1]].emplace_back(prerequisit[0]);
        }
        
        for(int i = 0; i < numCourses; ++i) {
            if(isCyclic(i, courses, path)) return false;
        }
        return true;        
    }
    
    bool isCyclic(int course, vector<vector<int>>& courses, vector<bool>& path) {
        if(path[course]) return true;
        
        path[course] = true;
        for(const auto& nextCourse: courses[course]) {
            if(isCyclic(nextCourse, courses, path)) 
                return true;
        }
        path[course] = false;
        return false;
    }
};
```

# Solution 2: Backtracking / Postorder DFS
## 思路

因為會判斷重複的路徑，Solution 1 有機會超時。
我們可以用兩個陣列來判斷：
1. 目前這次 DFS 的路徑是否有走過重複的點
2. 之前的 DFS 中是否有走過重複的點

例如這種狀況，我們第一次檢查過 0 -> 4 的路徑，後續就毋須再檢查 1 -> 4 的路徑，直接回傳 false 即可。

![](https://leetcode.com/problems/course-schedule/Figures/207/207_chain.png)

## 效能

### Complexity 
- Time Complexity: O(|E|+|V|), where |V| is the number of courses, and |E| is the number of dependencies.
- Space Complexity: O(|E|+|V|), with the same denotation as in the above time complexity.

### LeetCode Result
- Runtime: 12 ms
- Memory Usage: 13.8 MB 
- https://leetcode.com/submissions/detail/621533150/

## Code
```cpp
class Solution {
public:
    bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
        vector<vector<int>> courses(numCourses, vector<int>());
        visited = vector<bool>(numCourses, false);
        path = vector<bool>(numCourses, false);
        
        for(auto& prerequisit: prerequisites) {
            courses[prerequisit[1]].emplace_back(prerequisit[0]);
        }
        
        for(int i = 0; i < numCourses; ++i) {
            if(!visited[i] && isCyclic(i, courses)) return false;
        }
        return true;        
    }
    
    bool isCyclic(int course, vector<vector<int>>& courses) {
        if(visited[course]) return false;
        visited[course] = true;
        path[course] = true;
        for(const auto& nextCourse: courses[course]) {
            if(path[nextCourse]) 
                return true;
            else if(!visited[nextCourse] && isCyclic(nextCourse, courses)) 
                return true;
        }
        path[course] = false;
        return false;
    }
    
private:
    vector<bool> visited;
    vector<bool> path;
};
```


# Solution 3: Topological Sort / BFS
## 思路

將課程的依賴性畫成一張圖，不難發現會是一張有向圖，而題目要問的，其實就是判斷這張圖是否是一張 DAG（Directed Acyclic Graph, 有向無環圖）

![](https://leetcode.com/problems/course-schedule/Figures/207/207_graph.png)

Topological Sort（拓樸排序法）便是可以將 DAG 進行排序的演算法。同時也能利用排序的結果來判斷目前的圖是否為 DAG。

![](https://i.imgur.com/sQlJZ9T.png)

Topological Sort 的過程如動畫：

![](https://i.imgur.com/xKQIunG.gif)

## 效能

### Complexity 
- Time Complexity: O(|E|+|V|), where |V|∣V∣ is the number of courses, and |E|∣E∣ is the number of dependencies.
- Space Complexity: O(|E|+|V|), with the same denotation as in the above time complexity.

### LeetCode Result
- Runtime: 18 ms
- Memory Usage: 13.3 MB 
- https://leetcode.com/submissions/detail/618896851/

## Code
```cpp
class Solution {
public:
    bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
        vector<vector<int>> graph(numCourses, vector<int>());
        vector<int> indegree(numCourses, 0);

        for(auto&& prerequisite: prerequisites) {
            graph[prerequisite[1]].emplace_back(prerequisite[0]);
            ++indegree[prerequisite[0]];
        }
        
        queue<int> q;
        for(int i = 0; i < numCourses; ++i) {
            if(indegree[i] == 0) q.push(i);
        }
        
        int count = 0;
        while(!q.empty()) {
            int course = q.front(); q.pop();
            for(const auto& item: graph[course]) {
                if(--indegree[item] == 0) q.push(item);
            }
            ++count;
        }
        return count == numCourses;
    }
};
```

