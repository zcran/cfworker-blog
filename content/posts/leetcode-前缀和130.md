---
title: "leetcode-前缀和130"
date: 2026-08-25T10:02:54+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 计在矩形格子里移动的路径数目

给你一个大小为 n 的字符串数组 grid，其中每个字符串 grid[i] 的长度为 m。字符 grid[i][j] 是以下符号之一：

'.'：该单元格可用。
'#'：该单元格被阻塞。

你想计算攀爬 grid 的不同路径数量。每条路径必须从最后一行（第 n - 1 行）的任何一个格子开始，并在第一行（第 0 行）结束。

但是，路径受到以下限制：

· 你只能从一个可用单元格移动到 另一个 可用单元格。
· 每次移动的 欧几里得距离至多 为 d，其中 d 是给定的整数参数。两个单元格 (r1, c1) 和 (r2, c2) 之间的欧几里得距离为 sqrt((r1 - r2)^2 + (c1 - c2)^2)。
· 每次移动要么留在同一行，要么移动到正上方的一行（从第 r 行到第 r - 1 行）。
· 你不能连续两次移动都留在同一行。如果你在一次移动中留在同一行（且该移动不是最后一次移动），你的下一次移动必须进入上一行。

返回一个整数，表示此类路径的数量。由于答案可能很大，请将其对 10^9 + 7 取余 后返回。


```
impl Solution {
    pub fn number_of_routes(grid: Vec<String>, d: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let n = grid.len();
        let m = grid[0].len();
        let d = d as usize;

        // 垂直移动的最大水平偏移量 K = floor(sqrt(d^2 - 1))
        // 当 d=0 时，K=0；d>=1 时按公式计算
        let k = if d > 0 && d * d > 1 {
            ((d * d - 1) as f64).sqrt() as usize
        } else {
            0
        };

        // next0 / next1 分别表示下一行（已处理行）的累计前缀和
        // 索引 1..=m 有效，0 作为哨兵
        let mut next0 = vec![0i64; m + 1];
        let mut next1 = vec![0i64; m + 1];

        // 从最后一行向上逐行处理（最后一行先处理）
        for i in (0..n).rev() {
            let row = grid[i].as_bytes();
            let mut cur0 = vec![0i64; m + 1];
            let mut cur1 = vec![0i64; m + 1];

            // ---- 垂直移动：从下一行进入当前行 ----
            if i < n - 1 {
                // 有下一行，从 next0/next1 转移
                for j in 1..=m {
                    if row[j - 1] == b'.' {
                        let l = if j > k { j - k } else { 1 };
                        let r = if j + k <= m { j + k } else { m };
                        // 下一行区间 [l, r] 的和（同时包括两种状态）
                        let val = (next0[r] - next0[l - 1] + next1[r] - next1[l - 1]) % MOD;
                        let val = if val < 0 { val + MOD } else { val };
                        cur0[j] = (cur0[j - 1] + val) % MOD;
                    } else {
                        cur0[j] = cur0[j - 1];
                    }
                }
            } else {
                // 最后一行：每个可用格子本身可作为起点（路径长度为 1）
                for j in 1..=m {
                    if row[j - 1] == b'.' {
                        cur0[j] = (cur0[j - 1] + 1) % MOD;
                    } else {
                        cur0[j] = cur0[j - 1];
                    }
                }
            }

            // ---- 水平移动：在当前行内移动（不能连续水平，排除原地） ----
            for j in 1..=m {
                if row[j - 1] == b'.' {
                    let l = if j > d { j - d } else { 1 };
                    let r = if j + d <= m { j + d } else { m };
                    // 左侧区间 [l, j-1] 和右侧区间 [j+1, r] 的总和
                    let left = (cur0[j - 1] - cur0[l - 1]) % MOD;
                    let right = (cur0[r] - cur0[j]) % MOD;
                    let val = (left + right) % MOD;
                    let val = if val < 0 { val + MOD } else { val };
                    cur1[j] = (cur1[j - 1] + val) % MOD;
                } else {
                    cur1[j] = cur1[j - 1];
                }
            }

            // 当前行状态成为下一轮（上一行）的下一行状态
            next0 = cur0;
            next1 = cur1;
        }

        // 第 0 行的总方案数 = 所有列的状态之和（前缀和最后一个元素）
        ((next0[m] + next1[m]) % MOD) as i32
    }
}
```
