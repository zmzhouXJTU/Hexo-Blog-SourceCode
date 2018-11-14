---
title: Leetcode刷题之三数之和
date: 2018-11-14 15:01:37
categories: [Leetcode刷题]
tags: [Leetcode, Python, 算法, 数组]
---

Leetcode刷题之路第二天，今天的题和昨天的题目类似，同样是和数组有关的题。哈哈，打算按照Leetcode官方上面的分类来刷题，这样比较好一些。好了，废话不多说，让我们来开始吧！
<!--more-->

---

## 三数之和(难度:Medium)

### 题目描述

给定一个包含n个整数的数组nums，判断nums中是否存在三个元素a, b, c, 使得 a+b+c=0？找出所有满足条件且不重复的三元组。

**注意：答案中不可以包含重复的三元组**

```Python

例如, 给定数组 nums = [-1, 0, 1, 2, -1, -4]，

满足要求的三元组集合为：
[
  [-1, 0, 1],
  [-1, -1, 2]
]
```

### 解题方案

> 思路一: **- 时间复杂度: O(N^3) - 空间复杂度: O(N)**
> 第一想法，先把nums排序，用三个`loop`，无法`AC`

```Python

class Solution(object):
    def threeSum(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        n = len(nums)
        res = []
        nums.sort()
        for i in range(n):
            for j in range(i,n):
                for k in range(j,n):
                    if nums[i] + nums[j] + nums[k] == 0 and j != i and k != j and k != i: 
                        curRes = [nums[i],nums[j],nums[k]]
                        if curRes not in res:
                            res.append(curRes)
    
        return res

```

> 思路二：**- 时间复杂度: O(N^3) - 空间复杂度: O(N)**

然后查了一下`2sum`，用`2sum`的花样，因为要排除重复以及输出是按照从小到大的输出:但是还是超时

```Python

class Solution(object):  # 此法也超时
    def threeSum(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        def twoSum(nums, target):
            """
            :type nums: List[int]
            :type target: int
            :rtype: List[int]
            """
            lookup = {}
            for num in nums:
                if target - num in lookup:
                    if (-target ,target - num, num) not in res:
                        res.append((-target ,target - num, num))
                lookup[num] = target - num

        n = len(nums)
        nums.sort()
        res = []
        for i in range(n):
            twoSum(nums[i+1:], 0-nums[i])
        return [list(i) for i in res]

```

>思路三：**- 时间复杂度: O(N^2) - 空间复杂度: O(N)**

谷歌看别人的代码，思路非常清晰的,运行起来比直接调用`Two Sum`快.

清晰的思路：

* 排序
* 固定左边，如果左边重复，继续
* 左右弄边界，去重，针对不同的左右边界情况处理

```Python

class Solution(object):
    def threeSum(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        n, res = len(nums), []
        nums.sort()
        for i in range(n):
            if i > 0 and nums[i] == nums[i-1]:   # 因为i=0这个元素会直接往下执行
                continue
            l, r = i+1, n-1
            while l < r:
                tmp = nums[i] + nums[l] + nums[r]
                if tmp == 0:
                    res.append([nums[i], nums[l], nums[r]])
                    l += 1
                    r -= 1
                    while l < r and nums[l] == nums[l-1]: 
                        l += 1
                    while l < r and nums[r] == nums[r+1]: 
                        r -= 1
                elif tmp > 0:
                    r -= 1
                else:
                    l += 1
        return res

```