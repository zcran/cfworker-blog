---
title: "leetcode-数学97"
date: 2026-10-07T20:39:35+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 统计有效序列数目

给你两个正整数 n 和 k。

一个 有效序列 是一个由 k 个正整数组成的序列，满足以下条件：

序列中所有整数的 和 等于 n。
序列中所有整数的 乘积 是 偶数 。

返回有效序列的数量。由于答案可能很大，请将其对 109 + 7 取余 后返回。

如果两个序列在任何下标处不同，则认为它们是 不同 的序列。例如，[1, 1, 2] 和 [1, 2, 1] 被认为是不同的序列。


```
impl Solution {
    pub fn count_valid_sequences(n: i32, k: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;

        // 快速幂：(a^b) % MOD
        fn mod_pow(mut a: i64, mut b: i64) -> i64 {
            let mut r = 1;
            while b > 0 {
                if b & 1 == 1 {
                    r = r * a % MOD;
                }
                a = a * a % MOD;
                b >>= 1;
            }
            r
        }

        // 组合数 C(n, k) % MOD（k 较小，直接用阶乘递推 + 费马小定理求逆元）
        fn comb(n: i64, k: i64) -> i64 {
            if k < 0 || k > n {
                return 0;
            }
            let k = k.min(n - k); // 取较小的一侧减少循环次数
            let (mut num, mut den) = (1i64, 1i64);
            for i in 0..k {
                num = num * (n - i) % MOD;
                den = den * (i + 1) % MOD;
            }
            num * mod_pow(den, MOD - 2) % MOD
        }

        let n = n as i64;
        let k = k as i64;

        // 总方案数 = 把 n 分成 k 个正整数的方案数 = C(n - 1, k - 1)
        let total = comb(n - 1, k - 1);

        // 乘积为奇数的方案：所有数都是奇数。
        // 设 k 个奇数之和为 n，则 n 与 k 必须同奇偶，
        // 否则方案数为 0；否则令每个数减 1 后除以 2，
        // 转化为把 (n - k) / 2 分成 k 个非负整数：C((n - k) / 2 + k - 1, k - 1)
        let odd_only = if (n - k) % 2 == 0 {
            comb((n - k) / 2 + k - 1, k - 1)
        } else {
            0
        };

        // 乘积为偶数的方案 = 总数 - 全奇数的方案
        ((total - odd_only + MOD) % MOD) as i32
    }
}
```
