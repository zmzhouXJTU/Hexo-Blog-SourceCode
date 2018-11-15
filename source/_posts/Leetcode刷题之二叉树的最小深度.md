---
title: Leetcode刷题之二叉树的最小深度
date: 2018-11-15 18:51:16
categories: [[Leetcode刷题]]
tags: [Leetcode, Python, 算法, 树]
---

今天是刷题的第三天呀！今天在逛牛客网的时候偶然发现牛客网上也有`Leetcode`专题，而且还有相关的题目的热度，瞬间觉得牛客网真的是程序员求职的良心网站(哈哈哈，牛客网没有给我广告费，手动滑稽)。以后决定就在牛客网刷`Leetcode`系列的题了，牛客网上面一共148道题，刷完了我觉得应该也就差不多了。好了，话不多说，让我们开始吧！今天刷的是牛客网上`Leetcode`热度指数最高的一题---Minimum Depth of Binary Tree(二叉树的最小深度)
<!--more-->

---

## 二叉树的最小深度(难度：Easy)

### 题目描述

给定一个二叉树，找出其最小深度。

最小深度是从根节点到最近叶子节点的最短路径上的节点数量。

**说明**: 叶子节点是指没有子节点的节点。

**示例:**
```Python

给定二叉树 [3,9,20,null,null,15,7],
    3
   / \
  9  20
    /  \
   15   7
返回它的最小深度  2.
```

### 解题方案

思路一：**- 时间复杂度: O(N) - 空间复杂度: O(1)**

>思路，看完题目我想当然的认为就是直接递归取最小的值，代码如下：

```Python
class Solution(object):
    def minDepth(self, root):
        """
        :type root: TreeNode
        :rtype: int
        """
        if not root:
            return 0
        return 1 + min(map(self.minDepth, (root.left, root.right)))
```

但是没过，有一种特殊情况就是

注意`leaf node`: 反正就是没有`left`和`right`的

比如下图

```Python
1
 \
  2
```
`2`是一个孩子节点

这种情况应该输出`2`而不是`1`

唯一的特殊情况就是上面这种了，因为`root`下只有一个左节点或者是右节点，这样另外一边的空节点并不算是`leaf node`

> leaf node: itself is not null but it has both children null

所以还是要养成多写`edge case`的好习惯，也许就帮你避免了`general`写法的特例,代码如下:

```Python
class Solution(object):
    def minDepth(self, root):
        """
        :type root: TreeNode
        :rtype: int
        """
        if not root:
            return 0
        elif root.left and root.right:
            return 1 + min(self.minDepth(root.left), self.minDepth(root.right))
        elif root.left:
            return 1 + self.minDepth(root.left)
        elif root.right:
            return 1 + self.minDepth(root.right)
        else:
            return 1
```

思路二：**- 时间复杂度: O(N) - 空间复杂度: O(1)**

或许写的更巧妙一些

```Python
class Solution(object):
    def minDepth(self, root):
        """
        :type root: TreeNode
        :rtype: int
        """
        if not root:
            return 0
        depth_under_root = map(self.minDepth, (root.left, root.right))
        return 1 + (min(depth_under_root) or max(depth_under_root))
```