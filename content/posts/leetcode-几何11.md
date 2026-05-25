---
title: "leetcode-几何11"
date: 2026-05-11T20:20:55+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 三维形体投影面积

在 n x n 的网格 grid 中，我们放置了一些与 x，y，z 三轴对齐的 1 x 1 x 1 立方体。

每个值 v = grid[i][j] 表示有一列 v 个正方体叠放在格子 (i, j) 上。

现在，我们查看这些立方体在 xy 、yz 和 zx 平面上的投影。

投影 就像影子，将 三维 形体映射到一个 二维 平面上。从顶部、前面和侧面看立方体时，我们会看到“影子”。

返回 所有三个投影的总面积 。

```
impl Solution {
    /// 函数式风格计算投影面积
    pub fn projection_area(grid: Vec<Vec<i32>>) -> i32 {
        let n = grid.len();

        // 俯视：非零元素个数
        let top = grid.iter()
            .flat_map(|row| row.iter())
            .filter(|&&v| v != 0)
            .count() as i32;

        // 前视：每行最大值之和
        let front = grid.iter()
            .map(|row| row.iter().max().unwrap())
            .sum::<i32>();

        // 侧视：每列最大值之和（利用迭代器生成列索引）
        let side = (0..n)
            .map(|col| grid.iter().map(|row| row[col]).max().unwrap())
            .sum::<i32>();

        top + front + side
    }
}
```
