---
title: "leetcode-矩阵39"
date: 2026-09-19T10:41:34+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 扣分后的最大得分

给你一个 m x n 的整数矩阵 points （下标从 0 开始）。一开始你的得分为 0 ，你想最大化从矩阵中得到的分数。

你的得分方式为：每一行 中选取一个格子，选中坐标为 (r, c) 的格子会给你的总得分 增加 points[r][c] 。

然而，相邻行之间被选中的格子如果隔得太远，你会失去一些得分。对于相邻行 r 和 r + 1 （其中 0 <= r < m - 1），选中坐标为 (r, c1) 和 (r + 1, c2) 的格子，你的总得分 减少 abs(c1 - c2) 。

请你返回你能得到的 最大 得分。

abs(x) 定义为：

如果 x >= 0 ，那么值为 x 。
如果 x < 0 ，那么值为 -x 。


```
impl Solution {
    pub fn max_points(points: Vec<Vec<i32>>) -> i64 {
        let cols = points[0].len();

        // dp[c] 表示处理到当前行为止、且当前行选择第 c 列时的最大得分
        let mut dp = vec![0i64; cols];

        for row in &points {
            // 预处理：对上一行的 dp 做左右两次扫描，
            // 得到 tmp[c] = max_{c'} (dp[c'] - |c - c'|)
            let mut tmp = dp.clone();

            // 从左到右：tmp[c] = max(tmp[c], tmp[c-1] - 1)
            for c in 1..cols {
                tmp[c] = tmp[c].max(tmp[c - 1] - 1);
            }
            // 从右到左：tmp[c] = max(tmp[c], tmp[c+1] - 1)
            for c in (0..cols - 1).rev() {
                tmp[c] = tmp[c].max(tmp[c + 1] - 1);
            }

            // 加上当前行选中格子的分数
            for c in 0..cols {
                dp[c] = tmp[c] + row[c] as i64;
            }
        }

        // 最后一行的最大值即为答案
        *dp.iter().max().unwrap()
    }
}
```
