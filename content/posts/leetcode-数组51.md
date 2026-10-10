---
title: "leetcode-数组51"
date: 2026-10-10T20:38:44+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 找到稳定山的下标

有 n 座山排成一列，每座山都有一个高度。给你一个整数数组 height ，其中 height[i] 表示第 i 座山的高度，再给你一个整数 threshold 。

对于下标不为 0 的一座山，如果它左侧相邻的山的高度 严格大于 threshold ，那么我们称它是 稳定 的。我们定义下标为 0 的山 不是 稳定的。

请你返回一个数组，包含所有 稳定 山的下标，你可以以 任意 顺序返回下标数组。


```
impl Solution {
    /// 返回所有稳定山的下标：左侧相邻山高度严格大于 `threshold`。
    ///
    /// 遍历下标 1..n，若 `height[i-1] > threshold`，则下标 `i` 稳定。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(n)（结果数组）。
    pub fn stable_mountains(height: Vec<i32>, threshold: i32) -> Vec<i32> {
        (1..height.len())
            .filter(|&i| height[i - 1] > threshold)
            .map(|i| i as i32)
            .collect()
    }
}
```
