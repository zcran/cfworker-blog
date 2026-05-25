---
title: "leetcode-记忆化17"
date: 2026-05-06T20:22:34+08:00
tags: ["leetcode", "记忆化"]
draft: false
---



## 切披萨的方案数


给你一个 rows x cols 大小的矩形披萨和一个整数 k ，矩形包含两种字符： 'A' （表示苹果）和 '.' （表示空白格子）。你需要切披萨 k-1 次，得到 k 块披萨并送给别人。

切披萨的每一刀，先要选择是向垂直还是水平方向切，再在矩形的边界上选一个切的位置，将披萨一分为二。如果垂直地切披萨，那么需要把左边的部分送给一个人，如果水平地切，那么需要把上面的部分送给一个人。在切完最后一刀后，需要把剩下来的一块送给最后一个人。

请你返回确保每一块披萨包含 至少 一个苹果的切披萨方案数。由于答案可能是个很大的数字，请你返回它对 10^9 + 7 取余的结果。

```
impl Solution {
    /// 切披萨问题（LeetCode 1444）
    /// 给定一个 `m x n` 的披萨（每个格子可能是苹果 'A' 或空白 '.'），
    /// 切 `k-1` 刀将其分成 `k` 块，每次切必须水平或垂直贯穿整个当前块，
    /// 且每块必须至少包含一个苹果。返回不同的切法数量（模 1e9+7）。
    pub fn ways(pizza: Vec<String>, k: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let k = k as usize; // 将 k 转为 usize 便于索引

        // 为了方便计算前缀和，先将披萨转换为字符矩阵，并做旋转处理。
        // 原代码中先对每行反转（`.rev()`），再整体反转（`.reverse()`），
        // 这相当于将披萨旋转 180 度。这样做的目的是使得后续动态规划中，
        // 子矩形的右下角坐标可以方便地映射为前缀和索引。
        // 尽管不是必要步骤，但为了与原始解法完全一致，这里保留该操作。
        let mut pizza: Vec<Vec<char>> = pizza
            .into_iter()
            .map(|s| s.chars().rev().collect())
            .collect();
        pizza.reverse();

        let rows = pizza.len();
        let cols = pizza[0].len();

        // 计算二维前缀和 `prefix[i][j]` 表示矩形 `[0, i) x [0, j)` 内的苹果总数
        let mut prefix = vec![vec![0i64; cols + 1]; rows + 1];
        for i in 1..=rows {
            for j in 1..=cols {
                let has_apple = if pizza[i - 1][j - 1] == 'A' { 1 } else { 0 };
                prefix[i][j] = has_apple
                    + prefix[i - 1][j]
                    + prefix[i][j - 1]
                    - prefix[i - 1][j - 1];
            }
        }

        /// 判断矩形 `(top, left) -> (bottom, right)` (闭区间，均从 0 开始) 是否至少包含一个苹果
        /// 注意：本解法中所有坐标都基于旋转后的披萨，`(0,0)` 对应原始披萨的右下角。
        fn has_apple(prefix: &[Vec<i64>], top: usize, left: usize, bottom: usize, right: usize) -> bool {
            // 前缀和公式：sum = prefix[bottom+1][right+1] - prefix[top][right+1] - prefix[bottom+1][left] + prefix[top][left]
            let total = prefix[bottom + 1][right + 1]
                - prefix[top][right + 1]
                - prefix[bottom + 1][left]
                + prefix[top][left];
            total > 0
        }

        // `dp[i][j]` 表示以 `(i, j)` 为右下角的子矩形（即从 `(0,0)` 到 `(i-1, j-1)`）
        // 在当前切刀数下的切法数量。初始状态（切 0 刀）：若整个子矩形有苹果则方案数为 1，否则为 0。
        let mut dp = vec![vec![0i64; cols + 1]; rows + 1];
        for i in 1..=rows {
            for j in 1..=cols {
                if has_apple(&prefix, 0, 0, i - 1, j - 1) {
                    dp[i][j] = 1;
                }
            }
        }

        // 循环切 `k-1` 刀，每切一刀更新 `dp` 表
        for _ in 1..k {
            let mut next = vec![vec![0i64; cols + 1]; rows + 1];

            // 枚举所有可能的子矩形右下角 `(i, j)`
            for i in 1..=rows {
                for j in 1..=cols {
                    let mut ways = 0i64;

                    // 尝试在 `(i, j)` 子矩形内进行最后一次水平切割
                    // 假设切割线位于第 `row` 行下方（0 <= row < i）
                    for row in 0..i {
                        if has_apple(&prefix, row, 0, i - 1, j - 1) {
                            ways = (ways + dp[row][j]) % MOD;
                        }
                    }

                    // 尝试在 `(i, j)` 子矩形内进行最后一次垂直切割
                    // 假设切割线位于第 `col` 列右侧（0 <= col < j）
                    for col in 0..j {
                        if has_apple(&prefix, 0, col, i - 1, j - 1) {
                            ways = (ways + dp[i][col]) % MOD;
                        }
                    }

                    next[i][j] = ways;
                }
            }
            dp = next;
        }

        // 最终答案为整个披萨矩形 `(0,0) -> (rows-1, cols-1)` 的切法数
        (dp[rows][cols] % MOD) as i32
    }
}
```
