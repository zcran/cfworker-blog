---
title: "leetcode-动态规划40"
date: 2026-10-05T20:39:56+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 统计不同回文子序列

给你一个字符串 s ，返回 s 中不同的非空回文子序列个数 。由于答案可能很大，请返回对 10^9 + 7 取余 的结果。

字符串的子序列可以经由字符串删除 0 个或多个字符获得。

如果一个序列与它反转后的序列一致，那么它是回文序列。

如果存在某个 i , 满足 ai != bi ，则两个序列 a1, a2, ... 和 b1, b2, ... 不同。


```
const MOD: i64 = 1_000_000_007;

impl Solution {
    pub fn count_palindromic_subsequences(s: String) -> i32 {
        let s = s.as_bytes();
        let n = s.len();
        if n == 0 {
            return 0;
        }

        // left[i]/right[i]：与 s[i] 同字符在左侧/右侧最近的位置；
        // 不存在时分别用 usize::MAX / n 表示
        let mut left = vec![usize::MAX; n];
        let mut right = vec![n; n];
        let mut last = [usize::MAX; 26];
        for i in 0..n {
            let c = (s[i] - b'a') as usize;
            left[i] = last[c];
            last[c] = i;
        }
        let mut last = [n; 26];
        for i in (0..n).rev() {
            let c = (s[i] - b'a') as usize;
            right[i] = last[c];
            last[c] = i;
        }

        // dp[i][j]：s[i..=j] 中不同非空回文子序列的数量
        let mut dp = vec![vec![0i64; n]; n];
        for i in 0..n {
            dp[i][i] = 1;
        }

        // 从右往左、区间长度递增递推
        for i in (0..n.saturating_sub(1)).rev() {
            for j in i + 1..n {
                dp[i][j] = if s[i] != s[j] {
                    // 两端不等：容斥，分别去掉一端再减去重复部分
                    (dp[i + 1][j] + dp[i][j - 1] - dp[i + 1][j - 1] + MOD) % MOD
                } else {
                    // 两端相同：设中间与 s[i] 最近的同字符位置为 l、r
                    let (l, r) = (right[i], left[j]);
                    if l > r {
                        // 中间没有同字符：新增"两端字符本身"及"两端夹空串"
                        (dp[i + 1][j - 1] * 2 + 2) % MOD
                    } else if l == r {
                        // 中间恰有一个：会与"仅以该字符为回文"重复，故只 +1
                        (dp[i + 1][j - 1] * 2 + 1) % MOD
                    } else {
                        // 中间有多个：减去内部重复统计部分
                        (dp[i + 1][j - 1] * 2 - dp[l + 1][r - 1] + MOD) % MOD
                    }
                };
            }
        }

        dp[0][n - 1] as i32
    }
}
```
