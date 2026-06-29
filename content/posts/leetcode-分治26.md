---
title: "leetcode-分治26"
date: 2026-06-13T10:51:39+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 填充特殊网格

给你一个非负整数 N，表示一个 2^N x 2^N 的网格。你需要用从 0 到 2^(2N) - 1 的整数填充网格，使其成为一个 特殊 网格。一个网格当且仅当满足以下 所有 条件时，才能称之为 特殊 网格：

· 右上角象限中的所有数字都小于右下角象限中的所有数字。
· 右下角象限中的所有数字都小于左下角象限中的所有数字。
· 左下角象限中的所有数字都小于左上角象限中的所有数字。
· 每个象限也都是一个特殊网格。

返回一个 2^N x 2^N 的特殊网格。

注意：任何 1x1 的网格都是特殊网格。

```
impl Solution {
    /// 生成特殊网格（基于分治思想的希尔伯特曲线风格矩阵）
    ///
    /// 通过递归分治将大网格划分为四个象限，每个象限由子网格经过数值偏移得到。
    /// 数值偏移量 `q` 保证了整个网格的数字范围连续且不重复。
    ///
    /// # 参数
    /// - `n`: 非负整数，控制网格大小（2^n × 2^n）
    ///
    /// # 返回
    /// 特殊排列的二维矩阵，包含从 0 到 4^n - 1 的所有整数
    ///
    /// # 示例
    /// ```
    /// use solution::Solution;
    /// assert_eq!(Solution::special_grid(0), vec![vec![0]]);
    /// assert_eq!(Solution::special_grid(1), vec![vec![3, 0], vec![2, 1]]);
    /// assert_eq!(
    ///     Solution::special_grid(2),
    ///     vec![
    ///         vec![15, 12, 3, 0],
    ///         vec![14, 13, 2, 1],
    ///         vec![11, 8, 7, 4],
    ///         vec![10, 9, 6, 5]
    ///     ]
    /// );
    /// ```
    pub fn special_grid(n: i32) -> Vec<Vec<i32>> {
        // 基础情况：1x1 网格，唯一值为 0
        if n == 0 {
            return vec![vec![0]];
        }

        let size = 1 << n;               // 当前网格边长：2^n
        let half_size = size >> 1;       // 子网格边长：2^(n-1)
        let offset = 1 << (2 * n - 2);   // 子网格数值跨度：4^(n-1)，用于计算象限偏移

        // 递归生成子网格（half_size × half_size）
        let child_grid = Self::special_grid(n - 1);

        // 初始化当前层级的网格
        let mut grid = vec![vec![0; size]; size];

        // 分治填充四个象限
        // 子网格数值记为 child[i][j]，四个象限的映射关系：
        // ┌─────────────┬─────────────┐
        // │ 左上角      │ 右上角      │
        // │ child+3*off │ child+0*off │
        // ├─────────────┼─────────────┤
        // │ 左下角      │ 右下角      │
        // │ child+2*off │ child+1*off │
        // └─────────────┴─────────────┘
        for i in 0..half_size {
            for j in 0..half_size {
                let base_value = child_grid[i][j];

                // 右上象限（偏移 0*offset）
                grid[i][j + half_size] = base_value;

                // 左上象限（偏移 3*offset）
                grid[i][j] = base_value + 3 * offset as i32;

                // 右下象限（偏移 1*offset）
                grid[i + half_size][j + half_size] = base_value + offset as i32;

                // 左下象限（偏移 2*offset）
                grid[i + half_size][j] = base_value + 2 * offset as i32;
            }
        }

        grid
    }
}
```
