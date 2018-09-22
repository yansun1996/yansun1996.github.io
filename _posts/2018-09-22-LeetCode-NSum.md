---
layout: post
title:  "[Leetcode] N-Sum"
categories: Leetcode Algorithm
tags:  LeetCode-Array LeetCode-HashTable LeetCode-TwoPointers
---

* content
{:toc}

[Leetcode] N-Sum related problems summary.





# Intro
LeetCode has several N-Sum related problems, including 2Sum, 3Sum, 4Sum and some problems with different input data structures. Here I make a summary about how to solve these problems.

# 2Sum
[LeetCode 1 - Two Sum](https://leetcode.com/problems/two-sum/) is the first problem in LeetCode. We need to find the index of two numbers that they add up to the target value. There are two basic solutions.
    
### Hash Table (Recommended for 2Sum)

Just iterate the whole array. Each time just visit a element ```x```, use a Hash Table to store its index and the value of ```target - x```. For the following element (says ```y```), if it could be found that the value ```y``` exists in the Hash Table, it means that we found a pair ```(x, y)``` that has the equation ```target - x = y (x + y == target)```. Then just return the indexes of two values.

```python
class Solution(object):
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        d = {}
        for i in range(len(nums)):
            if d.has_key(target-nums[i]):
                return d[target-nums[i]],i
            d[nums[i]] = i
        return "Cannot find!"
```

### Two Pointer

Another Solution is using Two Pointers. Firstly, Sort the array, don't forget to save the original index of each element. Then use two pointers to search two numbers that can add up to target value. When it could be found, return their original indexes.

```python
class Solution:
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        i, j = 0, len(nums)-1
        l = sorted(zip(nums, range(len(nums))))
        left, right = 0, len(l)-1
        while left<right:
            v = l[left][0]+l[right][0]
            if v == target:
                return [l[left][1], l[right][1]]
            if v < target:
                left += 1
            else:
                right -= 1
        return
```

# 3Sum

[3Sum problem](https://leetcode.com/problems/3sum/) requires solution to find 3 elements in array that can add up to target value. **Different from 2Sum problem, the return array should be the value of each element instead of the oringinal index of each element**. So Two pointers solution is preferred here and the basic logic behind the solution is similar to what it is in the solution for 2Sum.

```python
class Solution(object):
    def threeSum(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        if not nums or len(nums) < 3:
            return []
        nums.sort()
        ans = []
        for i in range(len(nums)-1):
            # In order to avoid duplicated compare
            if i > 0 and nums[i] == nums[i-1]:
                continue
        
            l, r = i+1, len(nums)-1
            while l < r:
                s = nums[i] + nums[l] + nums[r]
                if s == 0:
                    ans.append([nums[i], nums[l], nums[r]])
                    l += 1
                    r -= 1
                    while l < len(nums) and nums[l] == nums[l-1]:
                        l += 1
                elif s < 0:
                    l += 1
                else:
                    r -= 1
        return ans
```

# NSum

The solution for NSum related problem uses two pointers method and recursion plan. The following code is the sample solution for [4Sum](https://leetcode.com/problems/4sum).

```python
class Solution(object):
    def fourSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[List[int]]
        """
        nums.sort()
        ans = []
        self.search(nums, ans, [], 4, target)
        return ans
    
    def search(self, nums, ans, path, N, target):
        ''''
        Template for N-sum
        '''
        if len(nums) < N or N < 2:
            return
        size = len(nums)
        if N == 2:
            l, r = 0, size-1
            while l < r:
                if nums[l] + nums[r] == target:
                    ans.append(path + [nums[l], nums[r]])
                    l += 1
                    r -= 1
                    # In order to avoid duplicated compare
                    while l < r and nums[l] == nums[l-1]:
                        l += 1
                elif nums[l] + nums[r] > target:
                    r -= 1
                else:
                    l += 1
        else:
            # Pls pay attention to the range for i
            for i in range(size-N+1):
                if N * nums[0] > target or N * nums[-1] < target:
                    break
                if i > 0 and nums[i] == nums[i-1]:
                    continue
                self.search(nums[i+1:], ans, path+[nums[i]], N-1, target-nums[i])
```