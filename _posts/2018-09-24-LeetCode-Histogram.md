---
layout: post
title:  "[Leetcode] Histogram"
categories: Leetcode Algorithm
tags:  LeetCode-Array LeetCode-TwoPointers LeetCode-Stack
---

* content
{:toc}

[Leetcode] Histogram related problems summary.




# 1. Intro
There are several histogram related problems in LeetCode, such as [11. Container With Most Water](https://leetcode.com/problems/container-with-most-water), [84. Largest Rectangle in Histogram](https://leetcode.com/problems/largest-rectangle-in-histogram) and other related problems(like [42. Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water) and [85. Maximal Rectangle](https://leetcode.com/problems/maximal-rectangle)). They require us to find specific area in the histogram.

# 2. Container With Most Water
```
Given n non-negative integers a1, a2, ..., an , where each represents a point at coordinate (i, ai). n vertical lines are drawn such that the two endpoints of line i is at (i, ai) and (i, 0). Find two lines, which together with x-axis forms a container, such that the container contains the most water.
Note: You may not slant the container and n is at least 2.
```

![](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/07/17/question_11.jpg)

We can use two pointer solution to solve this problem.

 Set two pointers at the beginning and end of the array and use a variable to **save the width(distance)** between two pointers for the convience of calculating current area. Initially, the width should be ```len(height)-1```. Then start to decrease the width. Each time we get a new width, update the maximum area by

<center>

ans = max(ans, width * min(height[pointer1], height[pointer2]))

</center>

When the width is decreased to zero, the algorithm finished.

```python
class Solution(object):
    def maxArea(self, height):
        """
        :type height: List[int]
        :rtype: int
        """
        a, b, width, ans = 0, len(height)-1, len(height)-1, 0
        for w in range(width, 0, -1):
            if height[a] < height[b]:
                ans = max(ans, w * height[a])
                a += 1
            else:
                ans = max(ans, w * height[b])
                b -= 1
        return ans
```

# 3. Trapping Rain Water

```
Given n non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it is able to trap after raining.
```

![](http://www.leetcode.com/static/images/problemset/rainwatertrap.png)

```
The above elevation map is represented by array [0,1,0,2,1,0,1,3,2,1,2,1]. In this case, 6 units of rain water (blue section) are being trapped.
```

We could also use two pointer solutions for this problem ```(Says a = 0, b = len(height) - 1)```. We need to move two pointers to the center of the array. Before we move pointer, we need to find the minimum value of two pointers and move the one with minimum value ```minH```. During the motion of that pointer, if ```height[current_pointer]``` is less than or equal to```minH```, then it indicates that water could be accumulated here and we need to add these water to the final answer ```ans += minH - height[current_pointer]```. If  ```height[current_pointer]``` is larger than ```minH```, then stop moving this pointer. Start to move another pointer that follows the same rule. After finishing moving two pointers, they are all located in the new position. Go to another round to move two pointers until ```a >= b```.

```python
class Solution(object):
    def trap(self, height):
        """
        :type height: List[int]
        :rtype: int
        """
        # two pointer solution
        a, b, minH, water = 0, len(height)-1, 0, 0
        while a < b:
            while a < b and height[a] <= minH:
                water += minH - height[a]
                a += 1
            while a < b and height[b] <= minH:
                water += minH - height[b]
                b -= 1
            minH = min(height[a], height[b])
        return water
```

# 4. Largest Rectangle in Hist

```
Given n non-negative integers representing the histogram's bar height where the width of each bar is 1, find the area of largest rectangle in the histogram.
```

![](https://leetcode.com/static/images/problemset/histogram.png)

```
Above is a histogram where width of each bar is 1, given height = [2,1,5,6,2,3].
```

![](https://leetcode.com/static/images/problemset/histogram_area.png)

```
The largest rectangle is shown in the shaded area, which has area = 10 unit.
```

We need to use ```stack``` to solve this problem. The stack need to store the index and height of the column we visited. When we get a ```current column``` whose height is higher than the last one in the stack, add it into the stack. If not, start to pop the last element from the stack until the height of last element less than current height. We need to update the maximum area by ```ans = max(ans, max_area whose right boundary is current column, max_area whose left boundary is poped column)```. 

```python
class Solution(object):
    def largestRectangleArea(self, heights):
        """
        :type heights: List[int]
        :rtype: int
        """
        # The height in the stack will remain ascending order
        stack = []
        ans = 0
        heights.append(0)
        
        for curr_idx, curr_h in enumerate(heights):
            last_idx = curr_idx
            while stack and stack[-1][1] >= curr_h:
                last_idx, last_h = stack.pop()
                ans = max(
                        ans,
                        curr_h * (curr_idx - last_idx + 1),
                        last_h * (curr_idx - last_idx)
                        )
            stack.append((last_idx, curr_h))
        return ans
```

# 5. Maximal Rectangle

```
Given a 2D binary matrix filled with 0's and 1's, find the largest rectangle containing only 1's and return its area.
```

Some parts of the solution for this problem uses the solution for [Section 4](#4-largest-rectangle-in-histogram). We just iterate each line of the matrix and create histogram. If the element in the same column of last row is still ```one```, then the height of histogram of this column could plus one. If it is ```zero```, we need to reset the height of this column to be ```one```. After building histogram of each line, calculate the maximum area in this histogram and update the answer ```ans = max(ans, max_area_in_this_histogram)```.

```python
class Solution(object):
    def maximalRectangle(self, matrix):
        """
        :type matrix: List[List[str]]
        :rtype: int
        """
        if not matrix:
            return 0
        m, n, res = len(matrix), len(matrix[0]), 0
        for i in range(m):
            matrix[i] = [int(a) for a in matrix[i]]
            if i > 0:
                for j in range(n):
                    if matrix[i][j]: matrix[i][j] += matrix[i-1][j]
            res = max(res, self.largestRectangleArea(matrix[i]))
        return res
        
    def largestRectangleArea(self, heights):
        """
        :type heights: List[int]
        :rtype: int
        """
        heights = [int(a) for a in heights]
        stack = []
        ans = 0
        heights.append(0)
        
        for curr_idx, curr_h in enumerate(heights):
            last_idx = curr_idx
            while stack and stack[-1][1] >= curr_h:
                last_idx, last_h = stack.pop()
                ans = max(
                        ans,
                        curr_h * (curr_idx - last_idx + 1),
                        last_h * (curr_idx - last_idx)
                        )
            stack.append((last_idx, curr_h))
        return ans
```