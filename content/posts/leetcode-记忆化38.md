---
title: "leetcode-记忆化38"
date: 2026-05-06T20:22:34+08:00
tags: ["leetcode", "记忆化"]
draft: false
---


## 矩阵中的最长递增路径

给定一个 m x n 整数矩阵 matrix ，找出其中 最长递增路径 的长度。

对于每个单元格，你可以往上，下，左，右四个方向移动。 不能 在 对角线 方向上移动或移动到 边界外（即不允许环绕）。



```
impl Solution {
    /// 计算矩阵中最长严格递增路径的长度。
    /// 使用深度优先搜索 + 记忆化，从每个格子出发，向四个方向递归，
    /// 只有目标格子值严格大于当前格子时才允许移动。
    pub fn longest_increasing_path(matrix: Vec<Vec<i32>>) -> i32 {
        let m = matrix.len();
        let n = matrix[0].len();
        // dp[i][j] 存储从 (i,j) 出发的最长递增路径长度，0 表示未计算
        let mut dp = vec![vec![0; n]; m];

        // 四个方向：上、下、左、右（行偏移，列偏移）
        const DIRS: [(i32, i32); 4] = [(-1, 0), (1, 0), (0, -1), (0, 1)];

        /// 递归计算从 (x, y) 出发的最长递增路径长度（记忆化）
        fn dfs(
            matrix: &[Vec<i32>],
            dp: &mut [Vec<i32>],
            x: usize,
            y: usize,
            dirs: &[(i32, i32)],
        ) -> i32 {
            if dp[x][y] != 0 {
                return dp[x][y];
            }

            let m = matrix.len();
            let n = matrix[0].len();
            let cur_val = matrix[x][y];

            // 对每个有效方向且满足严格递增条件的邻居进行递归，取最大值
            let max_neighbor = dirs
                .iter()
                .filter_map(|&(dx, dy)| {
                    let nx = x as i32 + dx;
                    let ny = y as i32 + dy;
                    // 边界检查
                    if nx >= 0 && ny >= 0 && nx < m as i32 && ny < n as i32 {
                        let (nx, ny) = (nx as usize, ny as usize);
                        if cur_val > matrix[nx][ny] {
                            Some(dfs(matrix, dp, nx, ny, dirs))
                        } else {
                            None
                        }
                    } else {
                        None
                    }
                })
                .max()
                .unwrap_or(0);

            dp[x][y] = 1 + max_neighbor;
            dp[x][y]
        }

        // 遍历所有格子，计算并记录最长路径
        let mut max_path = 0;
        for i in 0..m {
            for j in 0..n {
                if dp[i][j] == 0 {
                    dfs(&matrix, &mut dp, i, j, &DIRS);
                }
                max_path = max_path.max(dp[i][j]);
            }
        }

        max_path
    }
}
```
