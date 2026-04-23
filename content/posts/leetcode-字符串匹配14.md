---
title: "leetcode-字符串匹配14"
date: 2026-04-20T20:32:10+08:00
tags: ["leetcode", "字符串匹配"]
draft: false
---


## 字符串转换

给你两个长度都为 n 的字符串 s 和 t 。你可以对字符串 s 执行以下操作：

·将 s 长度为 l （0 < l < n）的 后缀字符串 删除，并将它添加在 s 的开头。
比方说，s = 'abcd' ，那么一次操作中，你可以删除后缀 'cd' ，并将它添加到 s 的开头，得到 s = 'cdab' 。

给你一个整数 k ，请你返回 恰好 k 次操作将 s 变为 t 的方案数。

由于答案可能很大，返回答案对 109 + 7 取余 后的结果。

```
impl Solution {
    pub fn number_of_ways(s: String, t: String, k: i64) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let n = s.len() as i64;

        // ---------- 特殊情况：长度为 1 ----------
        if n == 1 {
            // 操作要求 0 < l < n，因此无法执行任何操作
            return if s == t && k == 0 { 1 } else { 0 };
        }

        // ---------- 快速剪枝：字符频率必须完全相同 ----------
        let mut freq = [0; 26];
        for b in s.bytes() {
            freq[(b - b'a') as usize] += 1;
        }
        for b in t.bytes() {
            freq[(b - b'a') as usize] -= 1;
        }
        if freq.iter().any(|&c| c != 0) {
            return 0;
        }

        // ---------- 找出所有使 s 循环移位后等于 t 的偏移量 d ----------
        let s2 = s.clone() + &s;
        let s_bytes = s2.as_bytes();
        let t_bytes = t.as_bytes();
        let m = t_bytes.len();

        // 构建 KMP 前缀函数
        let mut pi = vec![0; m];
        let mut j = 0;
        for i in 1..m {
            while j > 0 && t_bytes[i] != t_bytes[j] {
                j = pi[j - 1];
            }
            if t_bytes[i] == t_bytes[j] {
                j += 1;
            }
            pi[i] = j;
        }

        // 在 s2 的前 2n-1 个字符中搜索 t
        let mut matches = Vec::new();
        j = 0;
        for i in 0..(2 * n as usize - 1) {
            while j > 0 && s_bytes[i] != t_bytes[j] {
                j = pi[j - 1];
            }
            if s_bytes[i] == t_bytes[j] {
                j += 1;
            }
            if j == m {
                let start = i + 1 - m;
                if start < n as usize {
                    matches.push(start as i64);
                }
                j = pi[j - 1];
            }
        }

        if matches.is_empty() {
            return 0;
        }

        // ---------- 矩阵快速幂计算 a_k 与 b_k ----------
        // 状态定义：
        // a_i : i 步后位于偏移量 0 的方案数
        // b_i : i 步后位于任一特定非零偏移量的方案数（所有非零位置对称）
        let base = [
            0, (n - 1) % MOD,
            1, (n - 2) % MOD,
        ];

        // 2x2 矩阵乘法
        fn mat_mul(a: &[i64; 4], b: &[i64; 4]) -> [i64; 4] {
            [
                (a[0] * b[0] + a[1] * b[2]) % MOD,
                (a[0] * b[1] + a[1] * b[3]) % MOD,
                (a[2] * b[0] + a[3] * b[2]) % MOD,
                (a[2] * b[1] + a[3] * b[3]) % MOD,
            ]
        }

        let mut exp = k;
        let mut res = [1, 0, 0, 1]; // 单位矩阵
        let mut base_pow = base;
        while exp > 0 {
            if exp & 1 == 1 {
                res = mat_mul(&res, &base_pow);
            }
            base_pow = mat_mul(&base_pow, &base_pow);
            exp >>= 1;
        }

        // 初始状态向量 [a_0, b_0] = [1, 0]
        let a_k = (res[0] * 1 + res[1] * 0) % MOD;
        let b_k = (res[2] * 1 + res[3] * 0) % MOD;

        // ---------- 计算最终答案 ----------
        let has_zero = matches.contains(&0);
        let non_zero_count = matches.len() - if has_zero { 1 } else { 0 };

        let mut ans = (a_k * if has_zero { 1 } else { 0 }) % MOD;
        ans = (ans + b_k * (non_zero_count as i64) % MOD) % MOD;

        ans as i32
    }
}
```
