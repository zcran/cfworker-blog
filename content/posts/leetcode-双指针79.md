---
title: "leetcode-双指针79"
date: 2026-09-05T09:53:05+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 旋转盒子

给你一个 m x n 的字符矩阵 boxGrid ，它表示一个箱子的侧视图。箱子的每一个格子可能为：

'#' 表示石头
'*' 表示固定的障碍物
'.' 表示空位置

这个箱子被 顺时针旋转 90 度 ，由于重力原因，部分石头的位置会发生改变。每个石头会垂直掉落，直到它遇到障碍物，另一个石头或者箱子的底部。重力 不会 影响障碍物的位置，同时箱子旋转不会产生惯性 ，也就是说石头的水平位置不会发生改变。

题目保证初始时 boxGrid 中的石头要么在一个障碍物上，要么在另一个石头上，要么在箱子的底部。

请你返回一个 n x m 的矩阵，表示按照上述旋转后，箱子内的结果。


```
impl Solution {
    pub fn rotate_the_box(box_grid: Vec<Vec<char>>) -> Vec<Vec<char>> {
        let rows = box_grid.len();          // 原行数（旋转后列数）
        let cols = box_grid[0].len();       // 原列数（旋转后行数）
        let mut ans = vec![vec!['.'; rows]; cols];

        for i in 0..rows {
            let col_idx = rows - 1 - i;     // 原行 i 旋转后对应的列索引
            let mut bottom = cols - 1;      // 当前列可放置石头的底部位置

            // 从右向左扫描原行（对应垂直方向自底向上）
            for j in (0..cols).rev() {
                match box_grid[i][j] {
                    '*' => {
                        // 障碍物位置固定，并重置底部指针至其上方
                        ans[j][col_idx] = '*';
                        bottom = j - 1;
                    }
                    '#' => {
                        // 石头掉落到底部位置
                        ans[bottom][col_idx] = '#';
                        bottom -= 1;
                    }
                    _ => {} // '.' 保持为空，无需操作
                }
            }
        }

        ans
    }
}
```
