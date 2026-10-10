---
title: "leetcode-动态规划122"
date: 2026-10-05T20:40:02+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 范围中美丽整数的数目

给你正整数 low ，high 和 k 。

如果一个数满足以下两个条件，那么它是 美丽的 ：

偶数数位的数目与奇数数位的数目相同。

这个整数可以被 k 整除。

请你返回范围 [low, high] 中美丽整数的数目。


```
const MAX_LEN: usize = 10;

impl Solution {
    pub fn number_of_beautiful_integers(low: i32, high: i32, k: i32) -> i32 {
        Self::calc(high, k as usize) - Self::calc(low - 1, k as usize)
    }

    /// 统计 [0, high] 中美丽整数的个数
    fn calc(high: i32, k: usize) -> i32 {
        if high < 0 {
            return 0;
        }
        let s: Vec<u8> = high.to_string().bytes().map(|b| b - b'0').collect();
        let n = s.len();
        // diff 偏移 n，范围 [0, 2n]，用于表示「奇数位 - 偶数位」的差值
        let diff_size = 2 * n + 1;
        // memo[i][rem][diff]：已填 i 位、模 k 余 rem、奇偶差为 diff 时的方案数
        let mut memo = vec![vec![vec![-1i32; diff_size]; k]; n];
        Self::dfs(0, 0, n, true, false, &s, k, n, &mut memo)
    }

    #[allow(clippy::too_many_arguments)]
    fn dfs(
        i: usize, rem: usize, diff: usize,
        is_limit: bool, is_num: bool,
        s: &[u8], k: usize, n: usize,
        memo: &mut Vec<Vec<Vec<i32>>>,
    ) -> i32 {
        if i == n {
            // 合法数字：已开始填数、余数为 0、奇偶位数相等（diff == n）
            return if is_num && rem == 0 && diff == n { 1 } else { 0 };
        }
        if !is_limit && is_num && memo[i][rem][diff] != -1 {
            return memo[i][rem][diff];
        }

        let mut res = 0;

        // 分支一：跳过当前位（仅当尚未开始填数）
        if !is_num {
            res += Self::dfs(i + 1, rem, diff, false, false, s, k, n, memo);
        }

        // 分支二：枚举当前位填入的数字 d
        let up = if is_limit { s[i] } else { 9 };
        let start = if is_num { 0 } else { 1 }; // 首位不能为 0
        for d in start..=up {
            let new_rem = (rem * 10 + d as usize) % k;
            // d 为奇数则 diff +1，为偶数则 diff -1
            let new_diff = if d % 2 == 1 { diff + 1 } else { diff - 1 };
            res += Self::dfs(
                i + 1, new_rem, new_diff,
                is_limit && d == up, true,
                s, k, n, memo,
            );
        }

        if !is_limit && is_num {
            memo[i][rem][diff] = res;
        }
        res
    }
}
```
