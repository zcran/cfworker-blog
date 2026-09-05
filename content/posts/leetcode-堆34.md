---
title: "leetcode-堆34"
date: 2026-08-21T09:54:12+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 矩阵中最大的三个菱形和

给你一个 m x n 的整数矩阵 grid 。

菱形和 指的是 grid 中一个正菱形 边界 上的元素之和。本题中的菱形必须为正方形旋转45度，且四个角都在一个格子当中。下图是四个可行的菱形，每个菱形和应该包含的格子都用了相应颜色标注在图中。
![](../pic/pc73-q4-desc-2.png)

注意，菱形可以是一个面积为 0 的区域，如上图中右下角的紫色菱形所示。

请你按照 降序 返回 grid 中三个最大的 互不相同的菱形和 。如果不同的和少于三个，则将它们全部返回。

```
use std::collections::HashSet;

impl Solution {
    pub fn get_biggest_three(grid: Vec<Vec<i32>>) -> Vec<i32> {
        let m = grid.len();
        let n = grid[0].len();

        // diag1[i][j]: prefix sum along top-left to bottom-right diagonals
        let mut diag1 = vec![vec![0; n + 1]; m + 1];
        // diag2[i][j]: prefix sum along top-right to bottom-left diagonals
        let mut diag2 = vec![vec![0; n + 2]; m + 1];

        for i in 1..=m {
            for j in 1..=n {
                diag1[i][j] = diag1[i - 1][j - 1] + grid[i - 1][j - 1];
                diag2[i][j] = diag2[i - 1][j + 1] + grid[i - 1][j - 1];
            }
        }

        let mut sums = HashSet::new();

        for i in 0..m {
            for j in 0..n {
                // A single cell is also a valid rhombus (size 0)
                sums.insert(grid[i][j]);

                // Try larger rhombuses with top vertex at (i, j)
                let mut k = i + 2;
                while k < m {
                    let height = k - i;
                    let half = height / 2;

                    let top = (i, j);
                    let bottom = (k, j);
                    let left_col = j as i32 - half as i32;
                    let right_col = j + half;

                    if left_col < 0 || right_col >= n {
                        break;
                    }

                    let (tx, ty) = top;
                    let (bx, by) = bottom;
                    let lx = i + half;
                    let ly = left_col as usize;
                    let rx = i + half;
                    let ry = right_col;

                    // Sum of 4 edges (each edge includes both endpoints)
                    let mut sum = 0;
                    // Top-left edge (diag2): (tx, ty) -> (lx, ly)
                    sum += diag2[lx + 1][ly + 1] - diag2[tx][ty + 2];
                    // Top-right edge (diag1): (tx, ty) -> (rx, ry)
                    sum += diag1[rx + 1][ry + 1] - diag1[tx][ty];
                    // Bottom-left edge (diag1): (lx, ly) -> (bx, by)
                    sum += diag1[bx + 1][by + 1] - diag1[lx][ly];
                    // Bottom-right edge (diag2): (rx, ry) -> (bx, by)
                    sum += diag2[bx + 1][by + 1] - diag2[rx][ry + 2];
                    // Subtract 4 corners (each was counted twice)
                    sum -= grid[tx][ty] + grid[bx][by] + grid[lx][ly] + grid[rx][ry];

                    sums.insert(sum);
                    k += 2;
                }
            }
        }

        let mut result: Vec<i32> = sums.into_iter().collect();
        result.sort_unstable_by(|a, b| b.cmp(a));
        result.truncate(3);
        result
    }
}
```
