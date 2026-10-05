---
title: "leetcode-矩阵77"
date: 2026-09-19T10:41:37+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 网格中得分最大的路径

给你一个 m x n 的网格 grid，其中每个单元格包含以下值之一：0、1 或 2。另给你一个整数 k。

你从左上角 (0, 0) 出发，目标是到达右下角 (m - 1, n - 1)，只能向 右 或 下 移动。

每个单元格根据其值对路径有以下贡献：

值为 0 的单元格：分数增加 0，花费 0。
值为 1 的单元格：分数增加 1，花费 1。
值为 2 的单元格：分数增加 2，花费 1。

返回在总花费不超过 k 的情况下可以获得的 最大分数 ，如果不存在有效路径，则返回 -1。

注意： 如果到达最后一个单元格时总花费超过 k，则该路径无效。


```
impl Solution {
    pub fn max_path_score(grid: Vec<Vec<i32>>, k: i32) -> i32 {
        let m = grid.len();
        let n = grid[0].len();
        let k = k as usize;

        // dp[j][c] 表示到达当前行第 j 列、总花费恰好为 c 时的最大分数
        // 用 -1 表示该状态不可达（因为所有分数均 >= 0）
        let mut prev = vec![vec![-1; k + 1]; n]; // 上一行的状态
        let mut curr = vec![vec![-1; k + 1]; n]; // 当前行的状态

        for i in 0..m {
            // 每行开始时，将 curr 重置为不可达
            for j in 0..n {
                curr[j].fill(-1);
            }

            for j in 0..n {
                let val = grid[i][j];
                let cost = if val == 0 { 0 } else { 1 };
                let score = val;

                // 起点 (0, 0) 特殊处理
                if i == 0 && j == 0 {
                    curr[0][cost] = score;
                    continue;
                }

                // 从上方 (i-1, j) 转移
                if i > 0 {
                    for c_prev in 0..=k {
                        if prev[j][c_prev] != -1 {
                            let c_new = c_prev + cost;
                            if c_new <= k {
                                let new_score = prev[j][c_prev] + score;
                                if new_score > curr[j][c_new] {
                                    curr[j][c_new] = new_score;
                                }
                            }
                        }
                    }
                }

                // 从左侧 (i, j-1) 转移
                if j > 0 {
                    for c_prev in 0..=k {
                        if curr[j - 1][c_prev] != -1 {
                            let c_new = c_prev + cost;
                            if c_new <= k {
                                let new_score = curr[j - 1][c_prev] + score;
                                if new_score > curr[j][c_new] {
                                    curr[j][c_new] = new_score;
                                }
                            }
                        }
                    }
                }
            }

            // 当前行计算完毕，交换 prev 和 curr，为下一行做准备
            std::mem::swap(&mut prev, &mut curr);
        }

        // 最终 prev 保存的是最后一行的状态，终点为 prev[n - 1]
        // 在所有花费不超过 k 的状态中找最大分数
        let mut ans = -1;
        for c in 0..=k {
            if prev[n - 1][c] > ans {
                ans = prev[n - 1][c];
            }
        }
        ans
    }
}
```
