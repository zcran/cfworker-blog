---
title: "leetcode-前缀和98"
date: 2026-08-25T10:02:52+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 找出所有稳定的二进制数组 I

给你 3 个正整数 num_zeros ，num_ones 和 limit 。

一个 二进制数组 arr 如果满足以下条件，那么我们称它是 稳定的 ：

· 0 在 arr 中出现次数 恰好 为 num_zeros 。
· 1 在 arr 中出现次数 恰好 为 num_ones 。
· arr 中每个长度超过 limit 的 子数组 都 同时 包含 0 和 1 。

请你返回一个整数表示 稳定 二进制数组的 总 数目。

由于答案可能很大，将它对 10^9 + 7 取余 后返回。


```
impl Solution {
    pub fn number_of_stable_arrays(zero: i32, one: i32, limit: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let (z, o, l) = (zero as usize, one as usize, limit as usize);

        // dp0[i][j]: 以0结尾，用了i个0、j个1的稳定数组数
        // dp1[i][j]: 以1结尾，用了i个0、j个1的稳定数组数
        let mut dp0 = vec![vec![0i64; o + 1]; z + 1];
        let mut dp1 = vec![vec![0i64; o + 1]; z + 1];

        // 初始化：只有一种数字的情况
        for i in 0..=z.min(l) {
            dp0[i][0] = 1; // 全是0
        }
        for j in 0..=o.min(l) {
            dp1[0][j] = 1; // 全是1
        }

        for i in 1..=z {
            for j in 1..=o {
                // 以0结尾：前一个字符是0 或 前一个字符是1
                dp0[i][j] = dp0[i - 1][j] + dp1[i - 1][j];
                if i > l {
                    // 减去末尾连续l+1个0的情况
                    dp0[i][j] -= dp1[i - l - 1][j];
                }

                // 以1结尾：前一个字符是1 或 前一个字符是0
                dp1[i][j] = dp1[i][j - 1] + dp0[i][j - 1];
                if j > l {
                    // 减去末尾连续l+1个1的情况
                    dp1[i][j] -= dp0[i][j - l - 1];
                }

                dp0[i][j] = (dp0[i][j] % MOD + MOD) % MOD;
                dp1[i][j] = (dp1[i][j] % MOD + MOD) % MOD;
            }
        }

        ((dp0[z][o] + dp1[z][o]) % MOD) as i32
    }
}
```
