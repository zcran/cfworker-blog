---
title: "leetcode-动态规划166"
date: 2026-10-05T20:40:05+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 给定范围内平衡整数的数目

给你两个整数 low 和 high。

如果一个整数同时满足以下 两个 条件，则称其为 平衡 整数：

它 至少 包含两位数字。
偶数位置上的数字之和 等于 奇数位置上的数字之和（最左边的数字位置为 1）。

返回一个整数，表示区间 [low, high]（包含两端）内平衡整数的数量。





```
impl Solution {
    pub fn count_balanced(low: i64, high: i64) -> i64 {
        if high < 11 {
            return 0;
        }
        let low = low.max(11);

        let low_s: Vec<i32> = low.to_string().bytes().map(|b| (b - b'0') as i32).collect();
        let high_s: Vec<i32> = high.to_string().bytes().map(|b| (b - b'0') as i32).collect();
        let n = high_s.len();
        let diff_lh = n - low_s.len();

        // offset 取最大可能负偏移量，即奇位数（ceil(n/2)）全部填 9
        let offset = ((n + 1) / 2 * 9) as i32;
        let size = (n * 9 + 1) as usize;

        let mut memo = vec![vec![-1i64; size]; n];

        fn dfs(
            i: usize,
            diff: i32,
            limit_low: bool,
            limit_high: bool,
            low_s: &[i32],
            high_s: &[i32],
            n: usize,
            diff_lh: usize,
            offset: i32,
            memo: &mut [Vec<i64>],
        ) -> i64 {
            if i == n {
                return (diff == 0) as i64;
            }

            let idx = (diff + offset) as usize;
            if !limit_low && !limit_high && memo[i][idx] >= 0 {
                return memo[i][idx];
            }

            let lo = if limit_low && i >= diff_lh {
                low_s[i - diff_lh]
            } else {
                0
            };
            let hi = if limit_high { high_s[i] } else { 9 };

            // 位置从 1 开始计数：i 为偶数（0-based）对应奇数位，取负
            let sign = if i % 2 == 0 { -1 } else { 1 };

            let mut res = 0i64;
            for d in lo..=hi {
                res += dfs(
                    i + 1,
                    diff + sign * d,
                    limit_low && d == lo,
                    limit_high && d == hi,
                    low_s,
                    high_s,
                    n,
                    diff_lh,
                    offset,
                    memo,
                );
            }

            if !limit_low && !limit_high {
                memo[i][idx] = res;
            }
            res
        }

        dfs(0, 0, true, true, &low_s, &high_s, n, diff_lh, offset, &mut memo)
    }
}
```
