---
layout: post
title:  "[Leetcode] Combinations"
categories: Leetcode Algorithm
tags:  LC-Array LC-DFS
---

* content
{:toc}

[Leetcode] Combination related problems summary.




# 1. Intro
There are several combinations related problems in LeetCode, such as [77. Combinations](https://leetcode.com/problems/combinations), [39.
Combination Sum](https://leetcode.com/problems/combination-sum) and other extended problems(like [40. Combination Sum II](https://leetcode.com/problems/combination-sum-ii), [216. Combination Sum III](https://leetcode.com/problems/combination-sum-iii) and [377. Combination Sum IV](https://leetcode.com/problems/combination-sum-iv/)).

# 2. Combinations

```Given two integers n and k, return all possible combinations of k numbers out of 1 ... n.```

For the number 1,2,3, ... , n, we need to give a list of all the combinations of k number.

### 2.1 Recursive DFS

We could use DFS to search all the combinations.

```python
class Solution(object):
    def combine(self, n, k):
        """
        :type n: int
        :type k: int
        :rtype: List[List[int]]
        """
        if not n or n < k:
            return []
        if n == k:
            return [[a for a in range(1,n+1)]]
        res, tmp = [], []
        self.dfs(res, tmp, 1, n, k)
        return res
    
    def dfs(self, res, tmp, idx, n, k):
        if len(tmp) == k:
            res.append(tmp)
            
        for i in range(idx,n+1):
            self.dfs(res, tmp+[i], i+1, n, k)
```

In this method, we initially check the special case such as n < k or n == k. Then use a list res to store all the results and use a list tmp to save temporary result. when the length of tmp is up to k, add current tmp (which contains a combination) to res. Finally, we return res for the final answer.

### 2.2 Iterative DFS

We could also implement iterative DFS solution.

```python
class Solution(object):
    def combine(self, n, k):
        ans = []
        stack = []
        x = 1
        while True:
            l = len(stack)
            if l == k:
                ans.append(stack[:])
            if l == k or x > n - k + l + 1:
                if not stack:
                    return ans
                x = stack.pop() + 1
            else:
                stack.append(x)
                x += 1
```

We use a stack to search and use ans to save results. For instance, if ```n = 5``` and ```k = 2```, the stack will be [1,1] ... [1,5] [1] [] [2] [2, 1] ... [2, 5] ... [4, 5] [4] []. Each time we reach to the value of n, the stack will pop the value in it and there is one value left in the stack. At the time when ```stack = [4, 5]```, 5 will be poped out since ```len(stack) == k``` and then ```x = 6```. Then ```x = stack.pop() + 1 = 5```. At this time we need to judge whether x > n - k + l + 1 = 5 - 2 + 0 + 1 = 4 and 5 > 4. In this way, the solution detect that stack is empty, the searching process ended.

### 2.3 Pythonic Solution
```python
class Solution(object):
    def combine(self, n, k):
        return list(itertools.combinations(range(1, n+1), k))
```

# 3. Combination Sum
```
Given a set of candidate numbers (candidates) (without duplicates) and a target number (target), find all unique combinations in candidates where the candidate numbers sums to target.

The same repeated number may be chosen from candidates unlimited number of times.

Note:

* All numbers (including target) will be positive integers.
* The solution set must not contain duplicate combinations.

Example:
Input: candidates = [2,3,5], target = 8,
A solution set is:
[
  [2,2,2,2],
  [2,3,3],
  [3,5]
]
```

We could use DFS to solve this problem.

```python
class Solution(object):
    def combinationSum(self, candidates, target):
        """
        :type candidates: List[int]
        :type target: int
        :rtype: List[List[int]]
        """
        if not candidates:
            return [[]]
        ans, path = [], []
        candidates.sort()
        self.dfs(candidates, target, ans, path, 0)
        return ans
    
    def dfs(self, candidates, target, ans, path, idx):
        if target < 0:
            return
        if target == 0:
            ans.append(path)
        for i in range(idx, len(candidates)):
            if candidates[i] > target:
                break
            self.dfs(candidates, target-candidates[i], ans, path+[candidates[i]], i)
```

Apart from DFS, we could also use some tricks to make our solution faster. For example, we could sort the candidates before the DFS process. Then during the searching, if the candidate we focused on is larger than remained target value, it means that this candidate and the candidate with larger value could not fit well in current combinations for the target sum. So there is no need to go through the whole loop and we could break to jump out the loop.

# 4. Combination Sum II
```
Given a collection of candidate numbers (candidates) and a target number (target), find all unique combinations in candidates where the candidate numbers sums to target.

Each number in candidates may only be used once in the combination.

Note:

All numbers (including target) will be positive integers.
The solution set must not contain duplicate combinations.
Example 1:

Example:
Input: candidates = [10,1,2,7,6,1,5], target = 8,
A solution set is:
[
  [1, 7],
  [1, 2, 5],
  [2, 6],
  [1, 1, 6]
]
```
Different from previous Combination Sum I problem, this problem requires that each element in candiadates can be used only once in the result.

```python
class Solution(object):
    def combinationSum2(self, candidates, target):
        """
        :type candidates: List[int]
        :type target: int
        :rtype: List[List[int]]
        """
        if not candidates:
            return [[]]
        candidates.sort()
        res, path, idx = [], [], 0
        self.dfs(candidates, res, target, path, idx)
        return res
    
    def dfs(self, candidates, res, target, path, idx):
        if target < 0:
            return
        if target == 0:
            res.append(path)
            return
        for i in range(idx, len(candidates)):
            if i > idx and candidates[i] == candidates[i-1]:
                continue
            if target - candidates[i] >= 0:
                self.dfs(candidates, res, target-candidates[i], path+[candidates[i]], i+1)
```

So each time when we initialize a new level for searching recursively, we need to make the next level search from ```idx + 1``` to avoid duplication.

# 5. Combination Sum III
```

Find all possible combinations of k numbers that add up to a number n, given that only numbers from 1 to 9 can be used and each combination should be a unique set of numbers.

Note:

All numbers will be positive integers.
The solution set must not contain duplicate combinations.

Example 1:
Input: k = 3, n = 7
Output: [[1,2,4]]
Example 2:

Example 2:
Input: k = 3, n = 9
Output: [[1,2,6], [1,3,5], [2,3,4]]
```

This problem is similar to previous two combination sum problems. Just need to add more check for specific restrictions such as whether the input ```k``` and ```n``` is reasonable.

```python
class Solution(object):
    def combinationSum3(self, k, n):
        """
        :type k: int
        :type n: int
        :rtype: List[List[int]]
        """
        nums = [a+1 for a in range(9)]
        if k > 9 or n < sum(nums[:k]) or n > sum(nums[-k:]):
            return []
        
        path, ans = [], []
        self.dfs(ans, path, n, k, 1)
        return ans
    
    def dfs(self, ans, path, n, k, idx):
        if n < 0:
            return
        if len(path) == k:
            if n == 0:
                ans.append(path)
            return
        for i in range(idx, 10):
            self.dfs(ans, path+[i], n-i, k, i+1)
```

# 6. Combination Sum IV
```
Given an integer array with all positive numbers and no duplicates, find the number of possible combinations that add up to a positive integer target.

Example:

nums = [1, 2, 3]
target = 4

The possible combination ways are:
(1, 1, 1, 1)
(1, 1, 2)
(1, 2, 1)
(1, 3)
(2, 1, 1)
(2, 2)
(3, 1)

Note that different sequences are counted as different combinations.

Therefore the output is 7.

Follow up:
What if negative numbers are allowed in the given array?
How does it change the problem?
What limitation we need to add to the question to allow negative numbers?
```

This problem is special since it identify the combination with diferent order as different situation. We could use either DFS or DP solution to get the correct number.

```python
# DFS solution
class Solution(object):
    def combinationSum4(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: int
        """
        nums.sort()
        cache = {}
        
        def dfs(target):
            if target in cache:
                return cache[target]
            c = 0
            for n in nums:
                if n > target:
                    break
                if n == target:
                    c += 1
                    break
                else:
                    c += dfs(target-n)
            cache[target] = c
            return c
            
        return dfs(target)
```

```python
# DP solution
class Solution(object):
    def combinationSum4(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: int
        """
        nums.sort()
        dp = [0] * (target+1)
        dp[0] = 1
        for i in range(1,target+1):
            print("i=%d")%i
            for n in nums:
                print("n=%d")%n
                if n>i:
                    break            
                else:
                    dp[i] += dp[i-n]
                print(dp)
        return dp[-1]
```