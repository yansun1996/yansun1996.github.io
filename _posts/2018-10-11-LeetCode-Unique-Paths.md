---
layout: post
title:  "[Leetcode] Unique Paths"
categories: Leetcode Algorithm
tags:  LC-DP
---

* content
{:toc}

[Leetcode] Summary for Unique Paths related problem.




# 1. Intro
There are two unique paths related problems in LeetCode, which is [62. Unique Paths](https://leetcode.com/problems/unique-paths) and [63. Unique Paths II](https://leetcode.com/problems/unique-paths-ii).

# 2. Unique Paths
```
A robot is located at the top-left corner of a m x n grid.
The robot can only move either down or right at any point in time.
The robot is trying to reach the bottom-right corner of the grid.
How many possible unique paths are there?
```
**Example:**
```
Input: m = 3, n = 2
Output: 3
Explanation:
From the top-left corner, there are a total of 3 ways to reach the bottom-right corner:
1. Right -> Right -> Down
2. Right -> Down -> Right
3. Down -> Right -> Right
```

A very simple dynamic programming solution could be used for this problem. The problem states that the robot could only move bottom or right. In this way, the first row and the very left column could be reached only in one way.

As for the other area in the matrix, the number of ways to reach them is defined by ```dp[i][j] = dp[i-1][j] + [i][j-1]```. In the end, we could get the results by the number of ways to reach the bottom-right area, which is ```dp[-1][-1]```.

```python
class Solution(object):
    def uniquePaths(self, m, n):
        """
        :type m: int
        :type n: int
        :rtype: int
        """
        dp = [[1 for _ in range(n)] for _ in range(m)]
        for i in range(1, m):
            for j in range(1, n):
                dp[i][j] = dp[i-1][j] + dp[i][j-1]
        return dp[-1][-1]
```

# 3. Unique Paths II
```
A robot is located at the top-left corner of a m x n grid.
The robot can only move either down or right at any point in time.
The robot is trying to reach the bottom-right corner of the grid.
Now consider if some obstacles are added to the grids. How many unique paths would there be?

Note: An obstacle and empty space is marked as 1 and 0 respectively in the grid.
```

**Example:**

```
Input:
[
  [0,0,0],
  [0,1,0],
  [0,0,0]
]
Output: 2
Explanation:
There is one obstacle in the middle of the 3x3 grid above.
There are two ways to reach the bottom-right corner:
1. Right -> Right -> Down -> Down
2. Down -> Down -> Right -> Right
```

Different from previous simple version problem, some obstacles are added to some areas. The similar dynamic programming solution could also be applied here.

Initially, we need to define the number of ways to reach the left and top edge. If one of areas on top edge has obstacle, it means that the rest area on the right side could not be reached anymore since the robot could only move bottom or right. This similar situation also exist on left edge. 

Then we need to detect how many ways to reach each area apart from top and left edge. The formula ```dp[i][j] = dp[i-1][j] + dp[i][j-1]``` is still the core function to calculate. However, when we find obstacle at one area, then the number of ways to reach it would be zero, which also leads to ```dp[i][j] = 0```.

In the end, we could just obtain the answer by reading the value of ```dp[-1][-1]```.

```python
class Solution(object):
    def uniquePathsWithObstacles(self, obstacleGrid):
        """
        :type obstacleGrid: List[List[int]]
        :rtype: int
        """
        m, n = len(obstacleGrid), len(obstacleGrid[0])
        dp = [[0 for _ in range(n)] for _ in range(m)]
        if obstacleGrid[0][0] or obstacleGrid[-1][-1]:
            return 0
        else:
            dp[0][0] = 1
            for i in range(1, m):
                if obstacleGrid[i][0]: break
                dp[i][0] = 1
            for j in range(1, n):
                if obstacleGrid[0][j]: break
                dp[0][j] = 1
            for i in range(1, m):
                for j in range(1, n):
                    if obstacleGrid[i][j]: continue
                    dp[i][j] = dp[i-1][j] + dp[i][j-1]
            return dp[-1][-1]
```
