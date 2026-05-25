---
title: "leetcode-记忆化29"
date: 2026-05-06T20:22:34+08:00
tags: ["leetcode", "记忆化"]
draft: false
---


## 矩阵中严格递增的单元格数

给你一个下标从 1 开始、大小为 m x n 的整数矩阵 mat，你可以选择任一单元格作为 起始单元格 。

从起始单元格出发，你可以移动到 同一行或同一列 中的任何其他单元格，但前提是目标单元格的值 严格大于 当前单元格的值。

你可以多次重复这一过程，从一个单元格移动到另一个单元格，直到无法再进行任何移动。

请你找出从某个单元开始访问矩阵所能访问的 单元格的最大数量 。

返回一个表示可访问单元格最大数量的整数。

```
use std::collections::HashMap;

impl Solution {
    /// 计算矩阵中最长严格递增路径的长度（可以按行或列任意移动，但不能斜向）
    /// 使用动态规划，按数值从小到大的顺序处理单元格。
    /// 维护每行、每列当前已知的最大路径长度，当处理到某个单元格时，
    /// 其最长路径 = max(所在行的最大值, 所在列的最大值) + 1。
    pub fn max_increasing_cells(mat: Vec<Vec<i32>>) -> i32 {
        let rows = mat.len();
        let cols = mat[0].len();

        // 将相同数值的单元格分组
        let mut value_to_cells: HashMap<i32, Vec<(usize, usize)>> = HashMap::new();
        for i in 0..rows {
            for j in 0..cols {
                value_to_cells.entry(mat[i][j]).or_default().push((i, j));
            }
        }

        // 行和列的最大长度数组
        let mut row_max = vec![0; rows];
        let mut col_max = vec![0; cols];

        // 按数值升序处理所有分组（复制键值，避免借用问题）
        let mut sorted_values: Vec<i32> = value_to_cells.keys().cloned().collect();
        sorted_values.sort();
        for val in sorted_values {
            let cells = &value_to_cells[&val]; // 使用引用作为 key
            // 计算当前组每个单元格的候选长度（基于当前行/列最大值）
            // 使用 map 收集结果，避免直接修改 row/col 导致同一组内互相影响
            let candidates: Vec<_> = cells
                .iter()
                .map(|&(i, j)| row_max[i].max(col_max[j]) + 1)
                .collect();

            // 更新行和列的最大值
            for (&(i, j), &len) in cells.iter().zip(candidates.iter()) {
                row_max[i] = row_max[i].max(len);
                col_max[j] = col_max[j].max(len);
            }
        }

        // 行或列中最大的长度即为答案
        *row_max.iter().max().unwrap()
    }
}
```
