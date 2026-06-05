---
title: "leetcode-组合数学7"
date: 2026-05-24T09:54:12+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 大小为 K 的不重叠线段的数目


给你一维空间的 n 个点，其中第 i 个点（编号从 0 到 n-1）位于 x = i 处，请你找到 恰好 k 个不重叠 线段且每个线段至少覆盖两个点的方案数。线段的两个端点必须都是 整数坐标 。这 k 个线段不需要全部覆盖全部 n 个点，且它们的端点 可以 重合。

请你返回 k 个不重叠线段的方案数。由于答案可能很大，请将结果对 10^9 + 7 取余 后返回。


```
impl Solution {
    pub fn number_of_sets(n: i32, k: i32) -> i32 {
        const MOD: i32 = 1_000_000_007;
        let n = n as usize;
        let k = k as usize;

        // 滚动数组：dp0[j] 表示当前点未结束区间，dp1[j] 表示当前点结束区间
        let mut dp0 = vec![0i32; k + 1];
        let mut dp1 = vec![0i32; k + 1];
        dp0[0] = 1; // 初始状态：在点0，0个区间，未结束

        for i in 1..n {
            let mut new0 = vec![0i32; k + 1];
            let mut new1 = vec![0i32; k + 1];
            for j in 0..=k {
                // 转移 f[i][j][0] = f[i-1][j][0] + f[i-1][j][1]
                new0[j] = (dp0[j] + dp1[j]) % MOD;
                // 转移 f[i][j][1] = f[i-1][j][1] + (j>0 ? f[i-1][j-1][0] + f[i-1][j-1][1] : 0)
                let mut val = dp1[j];
                if j > 0 {
                    val = (val + dp0[j - 1]) % MOD;
                    val = (val + dp1[j - 1]) % MOD;
                }
                new1[j] = val;
            }
            dp0 = new0;
            dp1 = new1;
        }

        // 最后一点可以是结束或未结束
        (dp0[k] + dp1[k]) % MOD
    }
}
```
