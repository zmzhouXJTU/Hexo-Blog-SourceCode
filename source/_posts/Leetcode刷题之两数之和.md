---
title: Leetcode刷题之两数之和
date: 2018-11-13 20:53:04
categories: [Leetcode刷题]
tags: [Leetcode, Python, 算法]
---

很久之前就有要写记录自己刷题的日记(博客)，奈何之前一直忙着没有太多时间，现在闲下来了，就赶紧把自己刷的题记录下来，哈哈。这里记录的是基于`Python`语言实现的`Leetcode`上面的算法题。好了，话不多说，让我们开始吧！
<!--more-->

---

## 两数之和

### 题目描述

给定一个整数数组和一个目标值，找出数组中和为目标值的两个数。

你可以假设每个输入只对应一种答案，且同样的元素不能被重复利用。

```Python
示例:

给定 nums = [2, 7, 11, 15], target = 9

因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]

```

### Python解答

```Python

class Solution(object):
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        dicts = {}
        for k, v in enumerate(nums):
            if target - v in dicts:
                return [dicts.get(target-v), k]
            dicts[v] = k

```
