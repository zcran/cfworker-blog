---
title: "leetcode-前缀和127"
date: 2026-08-25T10:02:54+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 连接非零数字并乘以其数字和 II

给你一个长度为 m 的字符串 s，其中仅包含数字。另给你一个二维整数数组 queries，其中 queries[i] = [li, ri]。

对于每个 queries[i]，提取 子串 s[li..ri]，然后执行以下操作：

· 将子串中所有 非零数字 按照原始顺序连接起来，形成一个新的整数 x。如果没有非零数字，则 x = 0。

· 令 sum 为 x 中所有数字的 数字和 。答案为 x * sum。

返回一个整数数组 answer，其中 answer[i] 是第 i 个查询的答案。

由于答案可能非常大，请返回其对 10^9 + 7 取余数的结果。

子串 是字符串中的一个连续、非空 字符序列。


```
const MOD: i64 = 1_000_000_007;

impl Solution {
    pub fn sum_and_multiply(s: String, queries: Vec<Vec<i32>>) -> Vec<i32> {
        let s = s.as_bytes();
        let n = s.len();

        // 预计算 10 的幂次
        let mut pow10 = vec![1i64; n + 1];
        for i in 1..=n {
            pow10[i] = (pow10[i - 1] * 10) % MOD;
        }

        // 前缀和数组
        // digit_sum[i] = s[0..i) 的数字和
        // num_val[i] = s[0..i) 中非零数字组成的数值（模 MOD）
        // non_zero_cnt[i] = s[0..i) 中非零数字的个数
        let mut digit_sum = vec![0i64; n + 1];
        let mut num_val = vec![0i64; n + 1];
        let mut non_zero_cnt = vec![0usize; n + 1];

        for i in 0..n {
            let d = (s[i] - b'0') as i64;
            digit_sum[i + 1] = digit_sum[i] + d;
            non_zero_cnt[i + 1] = non_zero_cnt[i] + (d > 0) as usize;

            if d > 0 {
                num_val[i + 1] = (num_val[i] * 10 + d) % MOD;
            } else {
                num_val[i + 1] = num_val[i];
            }
        }

        // 处理每个查询
        queries
            .iter()
            .map(|q| {
                let l = q[0] as usize;
                let r = q[1] as usize + 1; // 右端点转为开区间 [l, r)

                let len = non_zero_cnt[r] - non_zero_cnt[l];
                // 计算子串中非零数字组成的数值
                let x = (num_val[r] - (num_val[l] * pow10[len]) % MOD + MOD) % MOD;
                let sum = (digit_sum[r] - digit_sum[l]) % MOD;

                ((x * sum) % MOD) as i32
            })
            .collect()
    }
}
```
