---
title: "leetcode-前缀和105"
date: 2026-08-25T10:02:52+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 找到初始输入字符串 II

Alice 正在她的电脑上输入一个字符串。但是她打字技术比较笨拙，她 可能 在一个按键上按太久，导致一个字符被输入 多次 。

给你一个字符串 word ，它表示 最终 显示在 Alice 显示屏上的结果。同时给你一个 正 整数 k ，表示一开始 Alice 输入字符串的长度 至少 为 k 。

请你返回 Alice 一开始可能想要输入字符串的总方案数。

由于答案可能很大，请你将它对 10^9 + 7 取余 后返回。


```
impl Solution {
    pub fn possible_string_count(word: String, k: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let n = word.len();
        let k = k as usize;

        // 统计连续相同字符的段长度
        let mut segments = Vec::new();
        let chars = word.chars().collect::<Vec<_>>();
        let mut cnt = 1;
        for i in 1..n {
            if chars[i] == chars[i - 1] {
                cnt += 1;
            } else {
                segments.push(cnt);
                cnt = 1;
            }
        }
        segments.push(cnt);

        let m = segments.len();

        // 如果段数 >= k，任何组合都满足长度至少为 k
        if m >= k {
            let mut ans = 1i64;
            for &x in &segments {
                ans = (ans * x as i64) % MOD;
            }
            return ans as i32;
        }

        // dp[j]: 选择 j 段（每段至少选1个字符）的方案数
        let mut dp = vec![0i64; k];
        dp[0] = 1;
        let mut prefix = vec![1i64; k];

        for &len in &segments {
            let mut new_dp = vec![0i64; k];
            for j in 1..k {
                new_dp[j] = prefix[j - 1];
                if j > len as usize {
                    new_dp[j] = (new_dp[j] - prefix[j - len as usize - 1] + MOD) % MOD;
                }
            }
            dp = new_dp;

            // 前缀和优化
            prefix[0] = dp[0];
            for j in 1..k {
                prefix[j] = (prefix[j - 1] + dp[j]) % MOD;
            }
        }

        // 总方案数 - 长度 < k 的方案数
        let mut total = 1i64;
        for &x in &segments {
            total = (total * x as i64) % MOD;
        }
        let invalid = prefix[k - 1];
        ((total - invalid + MOD) % MOD) as i32
    }
}
```
