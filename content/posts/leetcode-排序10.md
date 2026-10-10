---
title: "leetcode-排序10"
date: 2026-10-05T00:35:00+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 最小操作次数使数组元素相等 II

给你一个长度为 n 的整数数组 nums ，返回使所有数组元素相等需要的最小操作数。

在一次操作中，你可以使数组中的一个元素加 1 或者减 1 。

测试用例经过设计以使答案在 32 位 整数范围内。


```
impl Solution {
    /// 使所有数组元素相等所需的最小操作数
    ///
    /// 思路：每次操作使某元素 ±1，总代价是各元素到目标值的距离之和；
    /// 该和在目标取「中位数」时最小（经典结论）。
    /// 用 select_nth_unstable 找中位数，平均 O(n)，无需完整排序。
    pub fn min_moves2(mut nums: Vec<i32>) -> i32 {
        let mid = nums.len() / 2;
        // 只需定位中位数，无需整体有序
        let median = *nums.select_nth_unstable(mid).1;

        nums.iter().map(|&x| (x - median).abs()).sum()
    }
}
```
