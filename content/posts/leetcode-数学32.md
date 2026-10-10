---
title: "leetcode-数学32"
date: 2026-10-07T20:39:30+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 分割字符串的方案数

给你一个二进制串 s  （一个只包含 0 和 1 的字符串），我们可以将 s 分割成 3 个 非空 字符串 s1, s2, s3 （s1 + s2 + s3 = s）。

请你返回分割 s 的方案数，满足 s1，s2 和 s3 中字符 '1' 的数目相同。

由于答案可能很大，请将它对 10^9 + 7 取余后返回。


```
impl Solution {
    pub fn num_ways(s: String) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let bytes = s.as_bytes();
        let n = bytes.len();

        // 无法分成 3 个非空子串
        if n < 3 {
            return 0;
        }

        let total_ones = bytes.iter().filter(|&&b| b == b'1').count();

        // 没有 '1'：在 n-1 个空隙中选 2 个切割点
        if total_ones == 0 {
            let n = n as i64;
            return ((n - 1) * (n - 2) / 2 % MOD) as i32;
        }

        // '1' 的个数不能被 3 整除，无法均分
        if total_ones % 3 != 0 {
            return 0;
        }

        let m = total_ones / 3;

        // 记录第 m、m+1、2m、2m+1 个 '1' 的索引
        let (mut idx_m, mut idx_m1, mut idx_2m, mut idx_2m1) = (0usize, 0usize, 0usize, 0usize);
        let mut cnt = 0usize;

        for (i, &b) in bytes.iter().enumerate() {
            if b == b'1' {
                cnt += 1;
                // 注意：当 m = 1 时，第 m+1 个和第 2m 个是同一个 '1'，
                // 因此必须使用独立的 if，不能写成 else if。
                if cnt == m {
                    idx_m = i;
                }
                if cnt == m + 1 {
                    idx_m1 = i;
                }
                if cnt == 2 * m {
                    idx_2m = i;
                }
                if cnt == 2 * m + 1 {
                    idx_2m1 = i;
                }
            }
        }

        // 第一段空隙长度 × 第二段空隙长度 即为方案数
        let ways = ((idx_m1 - idx_m) as i64) * ((idx_2m1 - idx_2m) as i64) % MOD;
        ways as i32
    }
}
```
