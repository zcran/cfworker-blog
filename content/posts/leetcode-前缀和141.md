---
title: "leetcode-前缀和141"
date: 2026-08-25T10:02:54+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 统计从两个字符串形成目标字符串的不同方案数

给你三个字符串 word1、word2 和 target。

你的任务是计算从 word1 和 word2 中选择字符以形成 target 的方案数，需满足以下条件：

· 对于 target 中的每个字符，从 word1 或 word2 中选择一个匹配的字符。
· 从 word1 中选择的下标必须是 严格 递增的。
· 从 word2 中选择的下标必须是 严格 递增的。
· 必须从 word1 和 word2 两者 中 至少 各选择一个字符。

如果对于 target 中的 至少 一个位置，选择的字符来自不同的字符串或不同的下标，则认为两种方案是不同的。

返回方案数。由于答案可能非常大，请返回它对 10^9 + 7 取余 后的结果。


```
impl Solution {
    const MOD: i64 = 1_000_000_007;

    pub fn interleave_characters(word1: String, word2: String, target: String) -> i32 {
        let s1 = word1.as_bytes();
        let s2 = word2.as_bytes();
        let t = target.as_bytes();
        let (n, m1, m2) = (t.len(), s1.len(), s2.len());

        // memo[i][j+1][k+1] 表示 dfs(i, j, k) 的答案，-1 表示未计算
        let mut memo = vec![vec![vec![-1i64; m2 + 1]; m1 + 1]; n];

        // 递归函数：考虑 target[0..=i]，word1[0..=j]，word2[0..=k] 的方案数
        fn dfs(
            i: i32,
            j: i32,
            k: i32,
            memo: &mut Vec<Vec<Vec<i64>>>,
            s1: &[u8],
            s2: &[u8],
            t: &[u8],
            mod_val: i64,
        ) -> i64 {
            // 剪枝：可用字符数不足
            if j < -1 || k < -1 || j + k + 1 < i {
                return 0;
            }
            if i < 0 {
                return 1; // target 为空，只有空方案
            }

            let idx_i = i as usize;
            let idx_j = (j + 1) as usize;
            let idx_k = (k + 1) as usize;

            if memo[idx_i][idx_j][idx_k] != -1 {
                return memo[idx_i][idx_j][idx_k];
            }

            // 容斥：不选 word1[j] 和 word2[k] 的所有情况
            let mut res = (dfs(i, j - 1, k, memo, s1, s2, t, mod_val)
                + dfs(i, j, k - 1, memo, s1, s2, t, mod_val)
                - dfs(i, j - 1, k - 1, memo, s1, s2, t, mod_val))
                % mod_val;
            if res < 0 {
                res += mod_val;
            }

            // 从 word1 中选字符
            if j >= 0 && s1[j as usize] == t[i as usize] {
                let add = (dfs(i - 1, j - 1, k, memo, s1, s2, t, mod_val)
                    - dfs(i - 1, j - 1, k - 1, memo, s1, s2, t, mod_val))
                    % mod_val;
                res = (res + add) % mod_val;
                if res < 0 {
                    res += mod_val;
                }
            }

            // 从 word2 中选字符
            if k >= 0 && s2[k as usize] == t[i as usize] {
                let add = (dfs(i - 1, j, k - 1, memo, s1, s2, t, mod_val)
                    - dfs(i - 1, j - 1, k - 1, memo, s1, s2, t, mod_val))
                    % mod_val;
                res = (res + add) % mod_val;
                if res < 0 {
                    res += mod_val;
                }
            }

            memo[idx_i][idx_j][idx_k] = res;
            res
        }

        // 计算总方案数（从两个字符串中任意选择）
        let total = dfs((n - 1) as i32, (m1 - 1) as i32, (m2 - 1) as i32, &mut memo, s1, s2, t, Self::MOD);

        // 只从 word1 中选的方案数
        let only1 = Self::num_distinct(s1, t);

        // 只从 word2 中选的方案数
        let only2 = Self::num_distinct(s2, t);

        // 容斥：至少各选一个
        let ans = (total - only1 - only2) % Self::MOD;
        if ans < 0 {
            (ans + Self::MOD) as i32
        } else {
            ans as i32
        }
    }

    // 计算 source 中形成 target 子序列的方案数（标准 DP）
    fn num_distinct(source: &[u8], target: &[u8]) -> i64 {
        let (n, m) = (source.len(), target.len());
        if n < m {
            return 0;
        }
        let mut dp = vec![0i64; m + 1];
        dp[0] = 1;
        for &ch in source {
            // 倒序更新，保证每个字符只用一次
            for j in (1..=m).rev() {
                if ch == target[j - 1] {
                    dp[j] = (dp[j] + dp[j - 1]) % Self::MOD;
                }
            }
        }
        dp[m]
    }
}
```
