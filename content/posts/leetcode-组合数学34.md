---
title: "leetcode-组合数学34"
date: 2026-05-24T09:54:14+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 统计小于 N 的 K 可约简整数


给你一个 二进制 字符串 s，它表示数字 n 的二进制形式。

同时，另给你一个整数 k。

如果整数 x 可以通过最多 k 次下述操作约简到 1 ，则将整数 x 称为 k-可约简 整数：

将 x 替换为其二进制表示中的置位数（即值为 1 的位）。

例如，数字 6 的二进制表示是 "110"。一次操作后，它变为 2（因为 "110" 中有两个置位）。再对 2（二进制为 "10"）进行操作后，它变为 1（因为 "10" 中有一个置位）。

返回小于 n 的正整数中有多少个是 k-可约简 整数。

由于答案可能很大，返回结果需要对 10^9 + 7 取余。

二进制中的置位是指二进制表示中值为 1 的位。


```
const MOD: i64 = 1_000_000_007;

impl Solution {
    pub fn count_k_reducible_numbers(s: String, k: i32) -> i32 {
        let s_bytes = s.as_bytes();
        let len = s_bytes.len();

        // 边界情况：n = 1，没有小于 1 的正整数
        if len == 1 {
            return 0;
        }

        // 1. 计算步数数组 steps[x]：将数值 x 通过操作变为 1 所需的次数
        //    steps[1] = 0，对于 x > 1：steps[x] = steps[popcount(x)] + 1
        let mut steps = vec![0; len]; // 只用到下标 0..len-1
        steps[1] = 0;
        for i in 2..len {
            let ones = i.count_ones() as usize;
            steps[i] = steps[ones] + 1;
        }

        // 2. 数位 DP：统计严格小于 n 的二进制数中，恰好有 `target_ones` 个 1 的个数
        //    记忆化数组 memo[pos][left1]：当前处理到第 pos 位，剩余需要放置的 1 的个数为 left1
        let mut memo = vec![vec![-1; len + 1]; len]; // -1 表示未计算
        let mut cnt_by_ones = vec![0i64; len]; // cnt_by_ones[c] = 恰好有 c 个 1 的数的个数

        // 递归函数定义（借用外部变量）
        fn dfs(
            pos: usize,
            left1: usize,
            is_limit: bool,
            s: &[u8],
            len: usize,
            memo: &mut Vec<Vec<i64>>,
        ) -> i64 {
            if pos == len {
                // 到达末尾：必须正好用完全部 1 且当前数严格小于 n
                return if !is_limit && left1 == 0 { 1 } else { 0 };
            }
            if !is_limit && memo[pos][left1] != -1 {
                return memo[pos][left1];
            }

            let up = if is_limit { (s[pos] - b'0') as usize } else { 1 };
            let mut res = 0;
            // 当前位可以选 0 或 1，但不能超过 up 且不能超出剩余的 1 的个数
            for d in 0..=up.min(left1) {
                res = (res + dfs(pos + 1, left1 - d, is_limit && d == up, s, len, memo)) % MOD;
            }
            if !is_limit {
                memo[pos][left1] = res;
            }
            res
        }

        // 对每个可能的 1 的个数 c 进行统计
        for c in 1..len {
            cnt_by_ones[c] = dfs(0, c, true, s_bytes, len, &mut memo);
        }

        // 3. 根据步数条件累加答案
        let k_usize = k as usize;
        let mut ans = 0i64;

        // 数字 1 本身（步数为 0）
        if k >= 0 {
            ans = (ans + 1) % MOD; // 因为 n > 1，所以 1 一定小于 n
        }

        // c = 1 的情况：除 1 以外，其它具有 1 个 1 的数的步数为 1 + steps[1] = 1
        if k_usize >= 1 {
            // cnt_by_ones[1] 包含了数字 1，需要减去它
            let extra = (cnt_by_ones[1] - 1 + MOD) % MOD;
            ans = (ans + extra) % MOD;
        }

        // c >= 2 的情况：步数 = 1 + steps[c]
        for c in 2..len {
            if 1 + steps[c] <= k_usize {
                ans = (ans + cnt_by_ones[c]) % MOD;
            }
        }

        ans as i32
    }
}
```
