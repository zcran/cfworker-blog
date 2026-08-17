---
title: "leetcode-计数93"
date: 2026-08-01T10:40:56+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 在矩阵上写出字母 Y 所需的最少操作次数

给你一个下标从 0 开始、大小为 n x n 的矩阵 grid ，其中 n 为奇数，且 grid[r][c] 的值为 0 、1 或 2 。

如果一个单元格属于以下三条线中的任一一条，我们就认为它是字母 Y 的一部分：

· 从左上角单元格开始到矩阵中心单元格结束的对角线。
· 从右上角单元格开始到矩阵中心单元格结束的对角线。
· 从中心单元格开始到矩阵底部边界结束的垂直线。

当且仅当满足以下全部条件时，可以判定矩阵上写有字母 Y ：

· 属于 Y 的所有单元格的值相等。
· 不属于 Y 的所有单元格的值相等。
· 属于 Y 的单元格的值与不属于Y的单元格的值不同。

每次操作你可以将任意单元格的值改变为 0 、1 或 2 。返回在矩阵上写出字母 Y 所需的 最少 操作次数。


```
impl Solution {
    pub fn minimum_operations_to_write_y(grid: Vec<Vec<i32>>) -> i32 {
        let n = grid.len();
        let m = n / 2;
        let mut y_cnt = [0; 3];      // Y形单元格中各颜色的计数
        let mut other_cnt = [0; 3];  // 非Y形单元格中各颜色的计数

        // 上半部分：两条斜对角线
        for i in 0..m {
            y_cnt[grid[i][i] as usize] += 1;
            y_cnt[grid[i][n - 1 - i] as usize] += 1;
            // 该行的非Y单元格（除了两个对角线位置）
            for j in 0..n {
                if j != i && j != n - 1 - i {
                    other_cnt[grid[i][j] as usize] += 1;
                }
            }
        }

        // 下半部分：垂直线 + 其余
        for i in m..n {
            y_cnt[grid[i][m] as usize] += 1;
            for j in 0..n {
                if j != m {
                    other_cnt[grid[i][j] as usize] += 1;
                }
            }
        }

        // 枚举Y的颜色(i)和非Y的颜色(j)，找最多不需要改变的单元格
        let mut max_keep = 0;
        for i in 0..3 {
            for j in 0..3 {
                if i != j {
                    max_keep = max_keep.max(y_cnt[i] + other_cnt[j]);
                }
            }
        }

        (n * n) as i32 - max_keep
    }
}
```
