---
title: 【LeetCode】207. Course Schedule 解題報告
categories:
  - LeetCode
date: 2022-01-17 14:56:56
tags: LeetCode
hidden: true
photos:
    - https://i.imgur.com/
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


## 效能

### Complexity 
- Time Complexity: O(|E|+|V|^2), where |V|∣V∣ is the number of courses, and |E|∣E∣ is the number of dependencies.
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


## 效能

### Complexity 
- Time Complexity: O(|E|+|V|), where |V|∣V∣ is the number of courses, and |E|∣E∣ is the number of dependencies.
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

