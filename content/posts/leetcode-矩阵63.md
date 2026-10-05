---
title: "leetcode-矩阵63"
date: 2026-09-19T10:41:36+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 使矩阵满足条件的最少操作次数

给你一个大小为 m x n 的二维矩形 grid 。每次 操作 中，你可以将 任一 格子的值修改为 任意 非负整数。完成所有操作后，你需要确保每个格子 grid[i][j] 的值满足：

如果下面相邻格子存在的话，它们的值相等，也就是 grid[i][j] == grid[i + 1][j]（如果存在）。
如果右边相邻格子存在的话，它们的值不相等，也就是 grid[i][j] != grid[i][j + 1]（如果存在）。

请你返回需要的 最少 操作数目。


```
impl Solution {
    pub fn minimum_operations(grid: Vec<Vec<i32>>) -> i32 {
        let m = grid.len();
        let n = grid[0].len();

        // cnt[j][v] 表示第 j 列中值为 v 的格子数量（值范围 0..=9）
        let mut cnt = vec![[0i32; 10]; n];
        for row in &grid {
            for j in 0..n {
                cnt[j][row[j] as usize] += 1;
            }
        }

        // memo[j][v]：处理到第 j 列，且第 j 列最终取值为 v 时，前 j 列最多能保留多少个格子不变。
        // v = 10 是一个虚拟值，表示“第 j 列没有取值约束”（用于最后一列的入口）。
        // 用 i32::MIN 表示尚未计算。
        let mut memo = vec![[i32::MIN; 11]; n];

        // 迭代式 DP，避免递归与函数指针开销：
        // dp[v] 表示当前列取值为 v 时，到目前为止最多能保留的格子数。
        // 初始时（第 0 列之前）没有任何约束，保留数为 0。
        let mut dp = [0i32; 11];

        for j in 0..n {
            let mut next = [i32::MIN; 11];
            // 枚举当前列的取值 v
            for v in 0..=10 {
                // 计算上一列在“不与 v 相同”的取值下的最大保留数
                let mut best_prev = i32::MIN;
                for prev in 0..=10 {
                    if prev != v {
                        if dp[prev] > best_prev {
                            best_prev = dp[prev];
                        }
                    }
                }
                // v = 10 是虚拟值，表示当前列不受“必须与下一列不同”的约束，
                // 此时它保留的格子数按 0 计（实际上不会选它作为真实取值）。
                let gain = if v < 10 { cnt[j][v] } else { 0 };
                next[v] = best_prev + gain;
            }
            dp = next;
        }

        // 最后一列可以取任意值，取 dp 中的最大值即为最多保留格子数。
        let max_keep = *dp.iter().max().unwrap();
        (m * n) as i32 - max_keep
    }
}
```
