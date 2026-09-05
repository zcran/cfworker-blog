---
title: "leetcode-前缀和102"
date: 2026-08-25T10:02:52+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 统计 X 和 Y 频数相等的子矩阵数量

给你一个二维字符矩阵 grid，其中 grid[i][j] 可能是 'X'、'Y' 或 '.'，返回满足以下条件的子矩阵数量：

包含 grid[0][0]
'X' 和 'Y' 的频数相等。
至少包含一个 'X'。



```
impl Solution {
    pub fn number_of_submatrices(grid: Vec<Vec<char>>) -> i32 {
        let (n, m) = (grid.len(), grid[0].len());
        let mut ans = 0;

        // prefix[i][j] = (x_count, y_count) 表示从 (0,0) 到 (i,j) 的矩形中 X 和 Y 的数量
        let mut prefix = vec![vec![(0, 0); m + 1]; n + 1];

        for i in 0..n {
            for j in 0..m {
                let (mut x, mut y) = (0, 0);
                match grid[i][j] {
                    'X' => x = 1,
                    'Y' => y = 1,
                    _ => {}
                }

                // 二维前缀和：当前 = 上方 + 左方 - 左上 + 自身
                let (top_x, top_y) = prefix[i][j + 1];
                let (left_x, left_y) = prefix[i + 1][j];
                let (diag_x, diag_y) = prefix[i][j];

                prefix[i + 1][j + 1] = (
                    top_x + left_x - diag_x + x,
                    top_y + left_y - diag_y + y,
                );

                let (cur_x, cur_y) = prefix[i + 1][j + 1];
                // 包含左上角且 X 和 Y 数量相等，且至少有一个 X
                if cur_x > 0 && cur_x == cur_y {
                    ans += 1;
                }
            }
        }

        ans
    }
}
```
