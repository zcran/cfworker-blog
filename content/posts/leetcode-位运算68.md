---
title: "leetcode-位运算68"
date: 2026-09-24T10:33:38+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 求出数组中最大序列值

给你一个整数数组 nums 和一个 正 整数 k 。

定义长度为 2 * x 的序列 seq 的 值 为：

(seq[0] OR seq[1] OR ... OR seq[x - 1]) XOR (seq[x] OR seq[x + 1] OR ... OR seq[2 * x - 1]).

请你求出 nums 中所有长度为 2 * k 的 子序列 的 最大值 。


```
impl Solution {
    /// 返回所有长度为 2k 的子序列的最大值。
    ///
    /// 枚举前后两半的分界点：正反两次"恰好选 k 个"的子集 OR 状压 DP
    /// （值域 < 128，用 u128 位图表示可达 OR 集合），再对每个分界点
    /// 枚举两侧 OR 值求最大异或。
    ///
    /// 时间复杂度 O(n·k·V + n·V^2)，V 为值域上界，空间复杂度 O(n + V)。
    pub fn max_value(nums: Vec<i32>, k: i32) -> i32 {
        let n = nums.len();
        let k = k as usize;

        // 值域 < 128，用 u128 位图表示 OR 值集合
        const MAX_V: usize = 128;

        // pre[m]：从 nums[0..m] 恰选 k 个的 OR 值集合（位掩码，bit i 表示 OR 值 i 可达）
        let mut pre: Vec<u128> = vec![0; n + 1];
        {
            let mut dp = vec![0u128; k + 1];
            dp[0] = 1;
            for i in 0..n {
                let v = nums[i] as u32;
                for j in (1..=k.min(i + 1)).rev() {
                    let mut src = dp[j - 1];
                    let mut acc = 0u128;
                    while src != 0 {
                        let b = src.trailing_zeros();
                        src &= src - 1;
                        acc |= 1u128 << ((b | v) as usize);
                    }
                    dp[j] |= acc;
                }
                pre[i + 1] = dp[k];
            }
        }

        // suf[m]：从 nums[m..n] 恰选 k 个的 OR 值集合
        let mut suf: Vec<u128> = vec![0; n + 1];
        {
            let mut dp = vec![0u128; k + 1];
            dp[0] = 1;
            for i in (0..n).rev() {
                let v = nums[i] as u32;
                for j in (1..=k.min(n - i)).rev() {
                    let mut src = dp[j - 1];
                    let mut acc = 0u128;
                    while src != 0 {
                        let b = src.trailing_zeros();
                        src &= src - 1;
                        acc |= 1u128 << ((b | v) as usize);
                    }
                    dp[j] |= acc;
                }
                suf[i] = dp[k];
            }
        }

        // 枚举分界点 m，左半 nums[0..m] 取 k 个，右半 nums[m..n] 取 k 个
        let mut best = 0i32;
        for m in k..=n - k {
            // 预处理右半所有可达 OR 值到数组，避免嵌套位扫描时重复解码
            let mut b_vals = [0i32; MAX_V];
            let mut b_len = 0;
            let mut b = suf[m];
            while b != 0 {
                b_vals[b_len] = b.trailing_zeros() as i32;
                b_len += 1;
                b &= b - 1;
            }

            let mut a = pre[m];
            while a != 0 {
                let av = a.trailing_zeros() as i32;
                a &= a - 1;
                for &bv in &b_vals[..b_len] {
                    best = best.max(av ^ bv);
                }
            }
        }
        best
    }
}
```
