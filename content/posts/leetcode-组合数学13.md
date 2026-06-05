---
title: "leetcode-组合数学13"
date: 2026-05-24T09:54:13+08:00
tags: ["leetcode", "组合数学"]
draft: false
---



##  恰有 K 根木棍可以看到的排列数目



有 n 根长度互不相同的木棍，长度为从 1 到 n 的整数。请你将这些木棍排成一排，并满足从左侧 可以看到 恰好 k 根木棍。从左侧 可以看到 木棍的前提是这个木棍的 左侧 不存在比它 更长的 木棍。

例如，如果木棍排列为 [1,3,2,5,4] ，那么从左侧可以看到的就是长度分别为 1、3 、5 的木棍。

给你 n 和 k ，返回符合题目要求的排列 数目 。由于答案可能很大，请返回对 10^9 + 7 取余 的结果。


```

impl Solution {
    const MOD: i64 = 1_000_000_007;

    pub fn rearrange_sticks(n: i32, k: i32) -> i32 {
        let n = n as usize;
        let k = k as usize;
        // 边界条件：k 为 0 或大于 n 时无有效排列
        if k == 0 || k > n {
            return 0;
        }

        // dp[j] 表示当前长度 i 的棍子中恰好有 j 根可见的方案数
        let mut f = vec![0i64; k + 1];
        f[0] = 1; // 0 根棍子有 0 根可见

        for i in 1..=n {
            let mut g = vec![0i64; k + 1];
            let limit = k.min(i); // 最多只能有 i 根可见
            for j in 1..=limit {
                // 递推公式：g[j] = f[j] * (i-1) + f[j-1]
                g[j] = (f[j] * (i as i64 - 1) % Self::MOD + f[j - 1]) % Self::MOD;
            }
            f = g; // 移动到下一行
        }

        f[k] as i32
    }
}
```
