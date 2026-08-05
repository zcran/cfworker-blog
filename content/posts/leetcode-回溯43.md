---
title: "leetcode-回溯43"
date: 2026-07-04T10:22:03+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 铺瓷砖

给定一个大小为 n x m 的长方形，返回贴满矩形所需的整数边正方形的最小数量。


```
impl Solution {
    pub fn tiling_rectangle(n: i32, m: i32) -> i32 {
        // 保证 n <= m，减小缓存维度
        if n > m {
            return Self::tiling_rectangle(m, n);
        }
        // memo[i][j] 表示 i x j 矩形的最小正方形数，0 表示未计算
        let mut memo = vec![vec![0; m as usize + 1]; n as usize + 1];
        Self::dfs(n, m, &mut memo)
    }

    /// 计算 n x m 矩形所需的最小正方形数（n <= m）
    fn dfs(n: i32, m: i32, memo: &mut Vec<Vec<i32>>) -> i32 {
        // 统一为 n <= m
        if n > m {
            return Self::dfs(m, n, memo);
        }

        // 边界情况
        if n == 0 {
            return 0;
        }
        if n == m {
            return 1; // 已是正方形
        }
        if n == 1 {
            return m; // 1 x m 需要 m 个 1x1
        }

        let (un, um) = (n as usize, m as usize);
        if memo[un][um] > 0 {
            return memo[un][um];
        }

        let mut best = i32::MAX;

        // ---- 一刀切切割（Guillotine）----
        // 在角落放置一个 i x i 正方形，剩余部分被一条直线分成两个矩形
        for i in 1..=n {
            // 方案 A：左上角放 i x i，右侧矩形 (n-i) x m，下方矩形 i x (m-i)
            let count_a = 1 + Self::dfs(n - i, m, memo) + Self::dfs(i, m - i, memo);
            best = best.min(count_a);

            // 方案 B：等价于在右上角放 i x i（旋转镜像），但为了覆盖所有方向保留
            let count_b = 1 + Self::dfs(n, m - i, memo) + Self::dfs(n - i, i, memo);
            best = best.min(count_b);
        }

        // ---- 非一刀切切割（非 Guillotine）----
        // 同时放置两个正方形，使剩余部分被分成三个矩形
        // 这种布局无法通过一次直线切割得到，但可以覆盖更多最优解
        for i in 1..n {
            let mut j = n - i + 1;
            while j < m - i && j < n {
                // 放置两个正方形，分别边长为 i 和 j（或类似），剩余三个矩形：
                // (n-i) x (m-j), (n-j) x (m-i), (i+j-n) x (m-i-j)
                let count_c = 2
                    + Self::dfs(n - i, m - j, memo)
                    + Self::dfs(n - j, m - i, memo)
                    + Self::dfs(i + j - n, m - i - j, memo);
                best = best.min(count_c);
                j += 1;
            }
        }

        memo[un][um] = best;
        best
    }
}
```
