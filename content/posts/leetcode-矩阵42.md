---
title: "leetcode-矩阵42"
date: 2026-09-19T10:41:34+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 获取单值网格的最小操作数

给你一个大小为 m x n 的二维整数网格 grid 和一个整数 x 。每一次操作，你可以对 grid 中的任一元素 加 x 或 减 x 。

单值网格 是全部元素都相等的网格。

返回使网格化为单值网格所需的 最小 操作数。如果不能，返回 -1 。


```
impl Solution {
    /// 核心思路：所有元素必须同余模 x，否则无解。
    /// 有解时，操作数最小等价于在排序后取中位数作为目标值（绝对值之和最小）。
    /// 时间复杂度：O(m*n*log(m*n))，空间复杂度：O(m*n)。
    pub fn min_operations(grid: Vec<Vec<i32>>, x: i32) -> i32 {
        // 展平网格，同时校验同余性
        let base = grid[0][0];
        let mut nums = Vec::with_capacity(grid.len() * grid[0].len());
        for row in &grid {
            for &v in row {
                if (v - base) % x != 0 {
                    return -1;
                }
                nums.push(v);
            }
        }

        // 排序后取中位数，最小化 Σ|v - mid|
        nums.sort_unstable();
        let mid = nums[nums.len() / 2];

        // 累加每个元素到中位数的操作次数
        nums.iter().map(|&v| (v - mid).abs() / x).sum()
    }
}
```
