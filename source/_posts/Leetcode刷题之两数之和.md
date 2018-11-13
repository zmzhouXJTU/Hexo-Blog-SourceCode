---
title: Leetcode刷题之两数之和
date: 2018-11-13 20:53:04
categories: [Leetcode刷题]
tags: [Leetcode, Python, 算法]
---

很久之前就有要写记录自己刷题的日记(博客)，奈何之前一直忙着没有太多时间，现在闲下来了，就赶紧把自己刷的题记录下来，哈哈。这里记录的是基于`Python`语言实现的`Leetcode`上面的算法题。好了，话不多说，让我们开始吧！
<!--more-->

---

## 两数之和(难度: Easy)

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

> 思路一：**- 时间复杂度: O(N^2) - 空间复杂度: O(1)**
> 暴力解法，两轮遍历
> beats：27.6%

```Python

class Solution(object):
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        for i in range(len(nums)):
            for j in range(i+1, len(nums)):
                if nums[i] + nums[j] == target:
                    return [i, j]

```

> 思路二：**- 时间复杂度: O(N) - 空间复杂度: O(N)**
> 上面的思路一太慢了，我们可以**牺牲空间换取时间**。

```Python

           2        7        11    15
         不存在   存在之中
lookup   {2:0}    [0，1]

```

* 建立字典`lookup`存放第一个数字，并存放该数字的`index`
* 判断 `lookup`种是否存在： `target - 当前数字`， 则表面当前值和`lookup`中的值加和为`target`
* 如果存在，则返回：`target - 当前数字`的`index`和当前值的`index`

> beats 100%

```Python

class Solution(object):
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        lookup = {}
        for i, num in enumerate(nums):
            if target - num in lookup:
                return [lookup[target-num], i]
            else:
                lookup[num] = i

```
