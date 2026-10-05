---
title: "leetcode-矩阵73"
date: 2026-09-19T10:41:36+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 子矩阵的最小绝对差

给你一个 m x n 的整数矩阵 grid 和一个整数 k。

对于矩阵 grid 中的每个连续的 k x k 子矩阵，计算其中任意两个 不同值 之间的 最小绝对差 。

返回一个大小为 (m - k + 1) x (n - k + 1) 的二维数组 ans，其中 ans[i][j] 表示以 grid 中坐标 (i, j) 为左上角的子矩阵的最小绝对差。

注意：如果子矩阵中的所有元素都相同，则答案为 0。

子矩阵 (x1, y1, x2, y2) 是一个由选择矩阵中所有满足 x1 <= x <= x2 且 y1 <= y <= y2 的单元格 matrix[x][y] 组成的矩阵。


```
impl Solution {
    pub fn min_abs_diff(grid: Vec<Vec<i32>>, k: i32) -> Vec<Vec<i32>> {
        let k = k as usize;
        let m = grid.len();
        let n = grid[0].len();
        let rows = m - k + 1;
        let cols = n - k + 1;

        let mut ans = vec![vec![0; cols]; rows];

        for i in 0..rows {
            for j in 0..cols {
                // 收集当前 k x k 子矩阵的所有元素
                let mut vals = Vec::with_capacity(k * k);
                for x in i..i + k {
                    vals.extend_from_slice(&grid[x][j..j + k]);
                }

                // 排序后，最小绝对差一定出现在相邻的不同值之间
                vals.sort_unstable();

                let mut best = i32::MAX;
                for t in 1..vals.len() {
                    let diff = vals[t] - vals[t - 1];
                    if diff != 0 && diff < best {
                        best = diff;
                    }
                }

                // 若所有元素相同，best 仍为 i32::MAX，此时答案为 0
                ans[i][j] = if best == i32::MAX { 0 } else { best };
            }
        }

        ans
    }
}
```
