---
title: "leetcode-排序34"
date: 2026-10-05T00:35:01+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 判断能否形成等差数列

给你一个数字数组 arr 。

如果一个数列中，任意相邻两项的差总等于同一个常数，那么这个数列就称为 等差数列 。

如果可以重新排列数组形成等差数列，请返回 true ；否则，返回 false 。


```
impl Solution {
    /// 判断数组能否重排成等差数列
    ///
    /// 思路：若存在等差数列排列，排序后必为唯一形态。
    /// 排序后检查相邻差是否恒为同一个常数即可。
    /// 时间 O(n log n)，额外空间 O(1)。
    pub fn can_make_arithmetic_progression(mut arr: Vec<i32>) -> bool {
        arr.sort_unstable();
        let d = arr[1] - arr[0]; // 公差
        arr.windows(2).all(|w| w[1] - w[0] == d)
    }
}
```
