---
title: "leetcode-矩阵61"
date: 2026-09-19T10:41:36+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 对角线上不同值的数量差

给你一个下标从 0 开始、大小为 m x n 的二维矩阵 grid ，请你求解大小同样为 m x n 的答案矩阵 answer 。

矩阵 answer 中每个单元格 (r, c) 的值可以按下述方式进行计算：

令 topLeft[r][c] 为矩阵 grid 中单元格 (r, c) 左上角对角线上 不同值 的数量。
令 bottomRight[r][c] 为矩阵 grid 中单元格 (r, c) 右下角对角线上 不同值 的数量。

然后 answer[r][c] = |topLeft[r][c] - bottomRight[r][c]| 。

返回矩阵 answer 。

矩阵对角线 是从最顶行或最左列的某个单元格开始，向右下方向走到矩阵末尾的对角线。

如果单元格 (r1, c1) 和单元格 (r, c) 属于同一条对角线且 r1 < r ，则单元格 (r1, c1) 属于单元格 (r, c) 的左上对角线。类似地，可以定义右下对角线。


```
use std::collections::HashSet;

impl Solution {
    pub fn difference_of_distinct_values(grid: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        let m = grid.len();
        let n = grid[0].len();
        let mut res = vec![vec![0; n]; m];

        // 1. 正向扫描：计算每个单元格左上对角线（不含自身）的不同值数量
        let mut top_left = vec![vec![0; n]; m];

        // 从最顶行开始的对角线
        for start_c in 0..n {
            let mut seen = HashSet::new();
            let (mut r, mut c) = (0, start_c);
            while r < m && c < n {
                top_left[r][c] = seen.len() as i32; // 此时 seen 只含左上方向的值
                seen.insert(grid[r][c]);            // 插入当前值，供后续单元格使用
                r += 1;
                c += 1;
            }
        }

        // 从最左列（除 (0,0)）开始的对角线
        for start_r in 1..m {
            let mut seen = HashSet::new();
            let (mut r, mut c) = (start_r, 0);
            while r < m && c < n {
                top_left[r][c] = seen.len() as i32;
                seen.insert(grid[r][c]);
                r += 1;
                c += 1;
            }
        }

        // 2. 反向扫描：计算右下对角线（不含自身）的不同值数量，并直接得出答案
        // 从最底行开始的对角线
        for start_c in 0..n {
            let mut seen = HashSet::new();
            let (mut r, mut c) = (m - 1, start_c);
            loop {
                let bottom_right = seen.len() as i32;
                res[r][c] = (top_left[r][c] - bottom_right).abs();
                seen.insert(grid[r][c]);
                if r == 0 || c == 0 {
                    break;
                }
                r -= 1;
                c -= 1;
            }
        }

        // 从最右列（除最底行）开始的对角线
        for start_r in 0..m - 1 {
            let mut seen = HashSet::new();
            let (mut r, mut c) = (start_r, n - 1);
            loop {
                let bottom_right = seen.len() as i32;
                res[r][c] = (top_left[r][c] - bottom_right).abs();
                seen.insert(grid[r][c]);
                if r == 0 || c == 0 {
                    break;
                }
                r -= 1;
                c -= 1;
            }
        }

        res
    }
}
```
