---
title: "leetcode-动态规划79"
date: 2026-10-05T20:39:59+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 给 N x 3 网格图涂色的方案数

你有一个 n x 3 的网格图 grid ，你需要用 红，黄，绿 三种颜色之一给每一个格子上色，且确保相邻格子颜色不同（也就是有相同水平边或者垂直边的格子颜色不同）。

给你网格图的行数 n 。

请你返回给 grid 涂色的方案数。由于答案可能会非常大，请你返回答案对 10^9 + 7 取余的结果。


```
impl Solution {
    /// 1411. 给 N x 3 网格图涂色的方案数
    ///
    /// 每一行的 3 个格子用红/黄/绿涂色，且行内相邻不同，只有两类合法模式：
    ///   ABA 型（两端同色，中间异色）：如 红黄红 → 共 6 种
    ///   ABC 型（三色互异）：如 红黄绿       → 共 6 种
    ///
    /// 相邻行之间，上一行为 ABA 时可接 2 种 ABA + 2 种 ABC；
    /// 上一行为 ABC 时可接 2 种 ABA + 3 种 ABC（由枚举得到）。
    /// 因此设 fi0 = ABA 型行数、fi1 = ABC 型行数，递推：
    ///   new_fi0 = 2*fi0 + 2*fi1
    ///   new_fi1 = 2*fi0 + 3*fi1
    ///
    /// 初始第 1 行两类各 6 种，迭代到第 n 行，答案为 fi0 + fi1。
    /// 时间 O(n)，空间 O(1)。
    pub fn num_of_ways(n: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;

        // fi0: ABA 型方案数，fi1: ABC 型方案数
        let (mut fi0, mut fi1) = (6i64, 6i64);

        for _ in 2..=n {
            let new_fi0 = (2 * fi0 + 2 * fi1) % MOD;
            let new_fi1 = (2 * fi0 + 3 * fi1) % MOD;
            fi0 = new_fi0;
            fi1 = new_fi1;
        }

        ((fi0 + fi1) % MOD) as i32
    }
}
```
