---
title: Leetcode刷题之二叉树的最大深度
date: 2018-11-16 15:39:49
categories: [Leetcode刷题]
tags: [Leetcode, Python, 算法, 树]
---

今天是刷题的第四天呀！接上昨天的那一篇博客，昨天在逛牛客网的时候偶然发现牛客网上也有`Leetcode`专题，一共选了148道题，而且还有相应的题目热度排名，于是决定就在牛客网刷`Leetcode`系列的题了，昨天刷的是牛客网上`Leetcode`热度指数最高的一题---Minimum Depth of Binary Tree(二叉树的最小深度)，用的是**递归**的思想，刷完自信心爆棚。于是今天准备刷第二道题---**逆波兰表达式求值**。直接打脸，不会做。于是本狗子灵机一动，按照**通过率**对题目排序，于是就说今天的这道题啦---maximum-depth-of-binary-tree(二叉树的最大深度)，也是递归的思想啦！
<!--more-->

---

## 二叉树的最大深度(难度：Easy)

### 题目描述

给定一个二叉树，找出其最大深度。

二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

**说明**: 叶子节点是指没有子节点的节点。

**示例：**

给定二叉树 [3,9,20,null,null,15,7]，

```Python
    3
   / \
  9  20
    /  \
   15   7
返回它的最大深度 3 。
```

### 解题方案

> 思路一：**- 时间复杂度: O(N) - 空间复杂度: O(1)**

简单题, 但是这道题跟昨天刷的`Leetcode 111`不一样，这道题没有特殊情况，所以一行就够了。以下给出代码：

```Python
class Solution(object):
    def maxDepth(self, root):
        """
        :type root: TreeNode
        :rtype: int
        """
        return 1 + max(map(self.maxDepth, (root.left, root.right))) if root else 0
```

### 后记

觉得博客这个东西应该是用来总结的，不应该当做刷题的日记。所以啊，这是最后一篇刷题的博文啦！我在`Github`上面专门建了一个仓库来记录我的`Leetcode`刷题之路，下面给出传送门：<https://github.com/zmzhouXJTU/LeetCode>，欢迎各位看官来踩！
