---
title: "leetcode-模拟63"
date: 2026-08-08T11:31:12+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 根据给定数字划分数组

给你一个下标从 0 开始的整数数组 nums 和一个整数 pivot 。请你将 nums 重新排列，使得以下条件均成立：

· 所有小于 pivot 的元素都出现在所有大于 pivot 的元素 之前 。
· 所有等于 pivot 的元素都出现在小于和大于 pivot 的元素 中间 。
· 小于 pivot 的元素之间和大于 pivot 的元素之间的 相对顺序 不发生改变。
    · 更正式的，考虑每一对 pi，pj ，pi 是初始时位置 i 元素的新位置，pj 是初始时位置 j 元素的新位置。如果 i < j 且两个元素 都 小于（或大于）pivot，那么 pi < pj 。

请你返回重新排列 nums 数组后的结果数组。


```
impl Solution {
    /// 稳定三分区：小于 pivot 的元素在前，等于 pivot 的居中，大于 pivot 的在后。
    pub fn pivot_array(nums: Vec<i32>, pivot: i32) -> Vec<i32> {
        let (mut less, mut equal, mut greater) = (Vec::new(), Vec::new(), Vec::new());

        for num in nums {
            if num < pivot {
                less.push(num);
            } else if num == pivot {
                equal.push(num);
            } else {
                greater.push(num);
            }
        }

        less.extend(equal);
        less.extend(greater);
        less
    }
}
```
