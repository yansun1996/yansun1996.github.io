---
layout: post
title:  "[Leetcode] Populating Next Right Pointers"
categories: Leetcode Algorithm
tags:  LC-Tree LC-BFS
---

* content
{:toc}

[Leetcode] Populating Next Right Pointers.




# 1. Intro
For a binary tree, in addition to left child and right child value. There are LeetCode problems asking about extra relationship among the nodes in the tree, including [116. Populating Next Right Pointers in Each Node](https://leetcode.com/problems/populating-next-right-pointers-in-each-node) and [117. Populating Next Right Pointers in Each Node II](https://leetcode.com/problems/populating-next-right-pointers-in-each-node-ii). These problems ask you to make the corresponded connection for all the nodes in the tree.

# 2. Populating Next Right Pointers
Given a binary tree like:
```
struct TreeLinkNode {
  TreeLinkNode *left;
  TreeLinkNode *right;
  TreeLinkNode *next;
}
```
Populate each next pointer to point to its next right node. If there is no next right node, the next pointer should be set to NULL.

Initially, all next pointers are set to NULL.

**```Note:```**

```
1. You may only use constant extra space.
2. Recursive approach is fine, implicit stack space does not count as extra space for this problem.
3. You may assume that it is a perfect binary tree (ie, all leaves are at the same level, and every parent has two children).
```
**```Example:```**

Given the following perfect binary tree,
```
     1
   /  \
  2    3
 / \  / \
4  5  6  7
```
After calling your function, the tree should look like:
```
     1 -> NULL
   /  \
  2 -> 3 -> NULL
 / \  / \
4->5->6->7 -> NULL
```

For this problem, we need a plan to go through the whole tree and make the correct next node connection for all nodes.  

```python
# Definition for binary tree with next pointer.
# class TreeLinkNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None
#         self.next = None

class Solution:
    # @param root, a tree link node
    # @return nothing
    def connect(self, root):
        if not root: return
        pre = root
        while root.left:
            root.left.next = root.right
            if root.next:
                root.right.next = root.next.left
                root = root.next
            else:
                root = pre.left
                pre = root
```

As for each level, we store ```the very left node``` as ```pre```, then go to deal with the nodes in this level one by one. Specifically, we need to make ```node.left.next = node.right```. This problem assumes that the input binary tree is perfect so we do not need to care whether ```node.right``` is None or not. Then we need to check whether ```node.next``` exists or not. 

If it exist, make ```node.right.next = node.next.left``` firstly and then go to the next node by ```node = node.next```. 

If ```node.next``` does not exist, it means that all nodes in this level have been visited and we need to go to the next level. Previously, we saved ```the very left node``` as ```pre```. At this point, we can go to the next level by ```node = pre.left```. What's more, do not forget to save ```the very left node``` as ```pre``` again.

When ```node.left``` is empty, it means that the whole process completed.

# 3. Populating Next Right Pointers II

Given a binary tree

```
struct TreeLinkNode {
  TreeLinkNode *left;
  TreeLinkNode *right;
  TreeLinkNode *next;
}
```

Populate each next pointer to point to its next right node. If there is no next right node, the next pointer should be set to NULL.

Initially, all next pointers are set to NULL.

**```Note:```**

```
1. You may only use constant extra space.
2. Recursive approach is fine, implicit stack space does not count as extra space for this problem.
```

**```Example:```**

Given the following binary tree,

```
     1
   /  \
  2    3
 / \    \
4   5    7
```

After calling your function, the tree should look like:

```
     1 -> NULL
   /  \
  2 -> 3 -> NULL
 / \    \
4-> 5 -> 7 -> NULL
```

Different from last question, it is possible that the input binary tree is not perfect, which means that we need to change the plan to go through the nodes on each level.

```python
# Definition for binary tree with next pointer.
# class TreeLinkNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None
#         self.next = None

class Solution:
    # @param root, a tree link node
    # @return nothing
    def connect(self, node):
        dummy = tail = TreeLinkNode(0)
        while node:
            tail.next = node.left
            if tail.next:
                tail = tail.next
            tail.next = node.right
            if tail.next:
                tail = tail.next
            node = node.next
            if not node:
                tail = dummy
                node = dummy.next
```

For each level, we initialize a dummy node and assume that the dummy node locates at the very left of each level. Then try to connect the nodes in this level by ```tail.next = node.left``` and ```tail.next = node.right```. Between these operations we also need to check whether the child nodes exist or not via ```tail = tail.next if tail.next```.

After going thorugh the left and right child nodes, we need to go to next node by ```node = node.next```, if ```node``` is ```None``` at this time, it means that it is time to go to the next level. Previously, we store the original location of ```tail``` as ```dummy``` and the operation ```tail.next = node.left``` also makes ```dummy.next = node.left```, so now we can go to next level by ```node = dummy.next``` since it is equal to ```node = dummy.next = node.left```.

When ```node``` is empty, it means that the populating process completed and we do not need to go to the next level.