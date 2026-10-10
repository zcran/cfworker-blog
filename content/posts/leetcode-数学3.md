---
title: "leetcode-数学3"
date: 2026-10-07T20:39:28+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 区间加法 II

给你一个 m x n 的矩阵 M 和一个操作数组 op 。矩阵初始化时所有的单元格都为 0 。ops[i] = [ai, bi] 意味着当所有的 0 <= x < ai 和 0 <= y < bi 时， M[x][y] 应该加 1。

在 执行完所有操作后 ，计算并返回 矩阵中最大整数的个数 。


```
impl Solution {
    /// 执行完所有操作后，返回矩阵中最大整数的个数。
    ///
    /// 每次操作 `[ai, bi]` 会将左上角 `ai × bi` 的子矩阵所有元素加 1。
    /// 因此某个单元格 (x, y) 的最终值等于所有满足 `ai > x && bi > y` 的操作数，
    /// 即被覆盖的次数。显然越靠左上角被覆盖次数越多，
    /// 最大整数出现在所有操作的交集区域内，其尺寸为：
    ///   min(m, a1, a2, ...) × min(n, b1, b2, ...)
    pub fn max_count(m: i32, n: i32, ops: Vec<Vec<i32>>) -> i32 {
        // 取所有操作在行、列方向上的最小值，初始为矩阵本身的维度
        let (mut min_a, mut min_b) = (m, n);
        for op in &ops {
            min_a = min_a.min(op[0]);
            min_b = min_b.min(op[1]);
        }
        min_a * min_b
    }
}
```
