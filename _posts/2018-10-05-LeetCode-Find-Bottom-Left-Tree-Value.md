---
layout: post
title:  "[Leetcode] Find Bottom Left Tree Value"
categories: Leetcode Algorithm
tags:  LC-Tree LC-DFS LC-BFS LC-Stack LC-Queue
---

* content
{:toc}

[Leetcode] 513 - Find Bottom Left Tree Value.




# 1. Intro
This problem ask you to find a specific position in a given binary tree. We need a plan to movev to the destination from the root node. Specifically, there are two kinds of plans, which are DFS and BFS.

# 2. DFS + Stack
We can use DFS + Stack to solve this problem.

```python
# DFS + Stack
class Solution(object):
    def findBottomLeftValue(self, root):
        """
        :type root: TreeNode
        :rtype: int
        """
        if not root:
            return
        max_depth = 0
        stack = [(root, 1)]

        while stack:
            curr, level = stack.pop()
            if curr:
                if level > max_depth:
                    max_depth = level
                    ans = curr.val
                stack.append((curr.right, level + 1))
                stack.append((curr.left, level + 1))
        return ans
```
In the stack we need to store the node and its level. Then we need to check whether its left and right children exist or not. The order of appending them into the stack must be ```firstly append right ---> then append left``` and then left will be poped first. **At the first time we met a node at a higher level, we need to pay more attention since it means that this node is the bottom left node at this level.** At this moment, we need to update the current level to this new higher level and store the temporary answer to the value of this node.

When the stack is empty, it indicates that all the nodes in the tree have been visited and we can just return the answer we find. The time complexity is O(n), space O(n).



# 3. BFS + Queue
We can also use BFS + Queue to solve this problem.

```python
# BFS + Queue (Faster)
class Solution(object):
    def findBottomLeftValue(self, root):
        """
        :type root: TreeNode
        :rtype: int
        """
        if not root: reutrn
        queue = [(root, 1)]
        ans, level = 0, 0
        while queue:
            node, curr_level = queue.pop(0)
            if curr_level > level:
                level = curr_level
                ans = node.val
            if node.left: queue.append((node.left, level+1))
            if node.right: queue.append((node.right, level+1))
        return ans
```
Diefferent from DFS solution, here we use queue that popes out the first element when needed. In addition, we also need to pay attention to the append order, which should be ```append left firstly ---> append right after```. When we get a new higher level element firstly, we need to update the current order and the answer since this node would be the bottom left node for the new higher level.

It is similar to the DFS method that when the stack is empty, all the nodes in the tree have been visited and we can just return the answer we find. The time complexity is O(n), space O(n).

