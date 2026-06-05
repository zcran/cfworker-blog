---
title: "leetcode-组合数学32"
date: 2026-05-24T09:54:14+08:00
tags: ["leetcode", "组合数学"]
draft: false
---

## 安排活动的方案数


给你三个整数 n ，x 和 y 。

一个活动总共有 n 位表演者。每一位表演者会 被安排 到 x 个节目之一，有可能有节目 没有 任何表演者。

所有节目都安排完毕后，评委会给每一个 有表演者的 节目打分，分数是一个 [1, y] 之间的整数。

请你返回 总 的活动方案数。

答案可能很大，请你将它对 10^9 + 7 取余 后返回。

注意 ，如果两个活动满足以下条件 之一 ，那么它们被视为 不同 的活动：

存在 一个表演者在不同的节目中表演。
存在 一个节目的分数不同。


```
impl Solution {
    pub fn number_of_ways(n: i32, x: i32, y: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;

        // 处理边界情况
        if n == 0 {
            return 1; // 没有表演者，只有一种方案（所有节目空置）
        }
        if x == 0 {
            return 0; // 没有节目，无法安排表演者
        }

        let n = n as usize;
        let x = x as i64;
        let y = y as i64;

        // 动态规划计算第二类斯特林数 S(n, i) 对于 i = 0..=n
        // 使用一维数组，递推公式：S(i, j) = S(i-1, j-1) + j * S(i-1, j)
        let mut stir = vec![0i64; n + 1];
        stir[0] = 1; // S(0, 0) = 1
        for i in 1..=n {
            // 逆序更新，避免覆盖本轮依赖的旧值
            for j in (1..=i).rev() {
                stir[j] = (stir[j - 1] + (j as i64) * stir[j]) % MOD;
            }
            stir[0] = 0; // S(i, 0) = 0 对于 i > 0
        }

        // 计算答案：Σ_{i=1}^{min(x,n)} P(x,i) * S(n,i) * y^i
        let mut ans = 0i64;
        let mut perm = 1i64;   // P(x, i)
        let mut pow_y = 1i64;  // y^i
        let max_i = x.min(n as i64) as usize;

        for i in 1..=max_i {
            perm = perm * (x - i as i64 + 1) % MOD;
            pow_y = pow_y * y % MOD;
            ans = (ans + perm * stir[i] % MOD * pow_y) % MOD;
        }

        ans as i32
    }
}
```
