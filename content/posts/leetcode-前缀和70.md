---
title: "leetcode-前缀和70"
date: 2026-08-25T10:02:50+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 二的幂数组中查询范围内的乘积

给你一个正整数 n ，你需要找到一个下标从 0 开始的数组 powers ，它包含 最少 数目的 2 的幂，且它们的和为 n 。powers 数组是 非递减 顺序的。根据前面描述，构造 powers 数组的方法是唯一的。

同时给你一个下标从 0 开始的二维整数数组 queries ，其中 queries[i] = [lefti, righti] ，其中 queries[i] 表示请你求出满足 lefti <= j <= righti 的所有 powers[j] 的乘积。

请你返回一个数组 answers ，长度与 queries 的长度相同，其中 answers[i]是第 i 个查询的答案。由于查询的结果可能非常大，请你将每个 answers[i] 都对 10^9 + 7 取余 。


```
impl Solution {
    pub fn product_queries(n: i32, queries: Vec<Vec<i32>>) -> Vec<i32> {
        const MOD: i64 = 1_000_000_007;

        // 将 n 分解为 2 的幂次，得到 powers 数组
        // 例如：n=13 (1101₂) => powers = [1, 4, 8]
        let mut powers = Vec::new();
        let mut bit = 1;
        let mut remaining = n;
        while remaining > 0 {
            if remaining & 1 == 1 {
                powers.push(bit);
            }
            remaining >>= 1;
            bit <<= 1;
        }

        // 预处理前缀积，加速区间乘积查询
        // prefix[i] 表示 powers[0..i) 的乘积
        let mut prefix = Vec::with_capacity(powers.len() + 1);
        prefix.push(1);
        for &p in &powers {
            prefix.push((prefix.last().unwrap() * p as i64) % MOD);
        }

        // 每个查询：区间 [l, r] 的乘积 = prefix[r+1] / prefix[l]
        // 由于是模素数，使用乘法逆元（或直接用前缀积相除取模）
        // 这里直接用累乘的方式更安全简洁
        queries.iter().map(|query| {
            let (l, r) = (query[0] as usize, query[1] as usize);
            let mut product = 1i64;
            for i in l..=r {
                product = (product * powers[i] as i64) % MOD;
            }
            product as i32
        }).collect()
    }
}
```
