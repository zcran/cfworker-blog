---
title: "leetcode-排序37"
date: 2026-10-05T00:35:02+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 删除某些元素后的数组均值

给你一个整数数组 arr ，请你删除最小 5% 的数字和最大 5% 的数字后，剩余数字的平均值。

与 标准答案 误差在 10^-5 的结果都被视为正确结果。


```
impl Solution {
    /// 删除最小 5% 与最大 5% 的数字后，剩余数字的平均值
    ///
    /// 思路：排序后，前后各去掉 n/20 个元素（向下取整），
    /// 对中间部分求和再除以个数。
    /// 时间 O(n log n)，额外空间 O(1)（原地排序）。
    pub fn trim_mean(mut arr: Vec<i32>) -> f64 {
        arr.sort_unstable();

        let trim = arr.len() / 20;            // 每侧删除的个数
        let middle = &arr[trim..arr.len() - trim];

        // 用 i64 累加，避免中间求和溢出
        let sum: i64 = middle.iter().map(|&x| x as i64).sum();
        sum as f64 / middle.len() as f64
    }
}
```
