---
title: "leetcode-前缀和73"
date: 2026-08-25T10:02:50+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 完美分割的方案数

给你一个字符串 s ，每个字符是数字 '1' 到 '9' ，再给你两个整数 k 和 minLength 。

如果对 s 的分割满足以下条件，那么我们认为它是一个 完美 分割：

s 被分成 k 段互不相交的子字符串。
每个子字符串长度都 至少 为 minLength 。
每个子字符串的第一个字符都是一个 质数 数字，最后一个字符都是一个 非质数 数字。质数数字为 '2' ，'3' ，'5' 和 '7' ，剩下的都是非质数数字。

请你返回 s 的 完美 分割数目。由于答案可能很大，请返回答案对 10^9 + 7 取余 后的结果。

一个 子字符串 是字符串中一段连续字符串序列。


```
impl Solution {
    pub fn beautiful_partitions(s: String, k: i32, min_length: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let s = s.as_bytes();
        let n = s.len();
        let k = k as usize;
        let min_len = min_length as usize;

        // 快速剪枝：长度不足 或 首字符不是质数 或 尾字符是质数
        if k * min_len > n || !is_prime(s[0]) || is_prime(s[n - 1]) {
            return 0;
        }

        // 预计算每个位置是否可以作为分割点
        // 分割点 j 表示在 s[j-1] 和 s[j] 之间切割（j 从 0 到 n）
        // can_cut[j] = true 表示 s[j-1] 非质数 且 s[j] 为质数（j=0 或 j=n 时特殊处理）
        let mut can_cut = vec![false; n + 1];
        can_cut[0] = true;  // 开头可以分割
        can_cut[n] = true;  // 结尾可以分割
        for j in 1..n {
            can_cut[j] = !is_prime(s[j - 1]) && is_prime(s[j]);
        }

        // dp[i][j] = 将前 j 个字符分割成 i 段的方案数
        // 使用滚动数组优化空间：只保留当前行和上一行
        let mut dp_prev = vec![0i64; n + 1];
        let mut dp_curr = vec![0i64; n + 1];
        dp_prev[0] = 1;

        for i in 1..=k {
            dp_curr.fill(0);
            let mut window_sum = 0i64;

            // 枚举当前段的结束位置 j
            // j 至少为 i * min_len，且剩余字符至少 (k-i) * min_len
            let start_j = i * min_len;
            let end_j = n - (k - i) * min_len;

            // 使用滑动窗口维护可以转移的前缀和
            // 当 j 增加时，新的可转移位置是 j - min_len
            for j in start_j..=end_j {
                // 将新的可转移位置加入窗口
                let new_pos = j - min_len;
                if can_cut[new_pos] {
                    window_sum = (window_sum + dp_prev[new_pos]) % MOD;
                }

                // 如果当前位置可以作为分割点，则更新 dp
                if can_cut[j] {
                    dp_curr[j] = window_sum;
                }
            }

            std::mem::swap(&mut dp_prev, &mut dp_curr);
        }

        dp_prev[n] as i32
    }
}

/// 判断字符是否为质数数字
#[inline]
fn is_prime(c: u8) -> bool {
    c == b'2' || c == b'3' || c == b'5' || c == b'7'
}
```
