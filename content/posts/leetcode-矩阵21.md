---
title: "leetcode-矩阵21"
date: 2026-09-19T10:41:33+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 最大的以 1 为边界的正方形

给你一个由若干 0 和 1 组成的二维网格 grid，请你找出边界全部由 1 组成的最大 正方形 子网格，并返回该子网格中的元素数量。如果不存在，则返回 0。


```
impl Solution {
    pub fn largest1_bordered_square(grid: Vec<Vec<i32>>) -> i32 {
        let m = grid.len();
        let n = grid[0].len();
        // left[i][j] 表示第 i 行中以 (i-1, j-1) 结尾的连续 1 的个数
        // up[i][j]   表示第 j 列中以 (i-1, j-1) 结尾的连续 1 的个数
        // 使用 (m+1) x (n+1) 便于处理边界，避免额外的越界判断
        let mut left = vec![vec![0usize; n + 1]; m + 1];
        let mut up = vec![vec![0usize; n + 1]; m + 1];
        let mut max_border = 0usize;

        for i in 1..=m {
            for j in 1..=n {
                if grid[i - 1][j - 1] == 1 {
                    left[i][j] = left[i][j - 1] + 1;
                    up[i][j] = up[i - 1][j] + 1;

                    // 以 (i-1, j-1) 为右下角，尝试的最大边长
                    let mut border = left[i][j].min(up[i][j]);
                    // 检查上边和左边是否能构成完整的边界
                    while left[i - border + 1][j] < border || up[i][j - border + 1] < border {
                        border -= 1;
                    }
                    max_border = max_border.max(border);
                }
            }
        }

        (max_border * max_border) as i32
    }
}
```
