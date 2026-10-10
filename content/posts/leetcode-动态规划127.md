---
title: "leetcode-动态规划127"
date: 2026-10-05T20:40:02+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 统计强大整数的数目

给你三个整数 start ，finish 和 limit 。同时给你一个下标从 0 开始的字符串 s ，表示一个 正 整数。

如果一个 正 整数 x 末尾部分是 s （换句话说，s 是 x 的 后缀），且 x 中的每个数位至多是 limit ，那么我们称 x 是 强大的 。

请你返回区间 [start..finish] 内强大整数的 总数目 。

如果一个字符串 x 是 y 中某个下标开始（包括 0 ），到下标为 y.length - 1 结束的子字符串，那么我们称 x 是 y 的一个后缀。比方说，25 是 5125 的一个后缀，但不是 512 的后缀。


```
impl Solution {
    pub fn number_of_powerful_int(start: i64, finish: i64, limit: i32, s: String) -> i64 {
        let low = format!("{:0>width$}", start, width = finish.to_string().len());
        let high = finish.to_string();
        let s = s.as_bytes();
        let n = high.len();
        let pre = n - s.len(); // 前缀长度（需要自由填数的部分）

        // 记忆化：memo[i] 表示在第 i 位且不受上下界限制时的方案数
        // 由于后缀 s 固定，dp 只与位置 i 有关
        let mut memo = vec![-1i64; n];
        let lo = low.as_bytes();
        let hi = high.as_bytes();

        fn dfs(
            i: usize, tight_low: bool, tight_high: bool,
            lo: &[u8], hi: &[u8], limit: i32, s: &[u8], pre: usize, n: usize,
            memo: &mut Vec<i64>,
        ) -> i64 {
            if i == n {
                return 1;
            }
            // 仅当不受上下界限制时可复用结果
            if !tight_low && !tight_high && memo[i] != -1 {
                return memo[i];
            }

            let low_d = if tight_low { lo[i] - b'0' } else { 0 };
            let high_d = if tight_high { hi[i] - b'0' } else { 9 };
            let mut res = 0i64;

            if i < pre {
                // 前缀部分：可以自由选择 0..=min(high_d, limit)
                let up = high_d.min(limit as u8);
                for d in low_d..=up {
                    res += dfs(
                        i + 1,
                        tight_low && d == low_d,
                        tight_high && d == high_d,
                        lo, hi, limit, s, pre, n, memo,
                    );
                }
            } else {
                // 后缀部分：必须与 s 对应位相同
                let d = s[i - pre] - b'0';
                if low_d <= d && d <= high_d && d <= limit as u8 {
                    res = dfs(
                        i + 1,
                        tight_low && d == low_d,
                        tight_high && d == high_d,
                        lo, hi, limit, s, pre, n, memo,
                    );
                }
            }

            if !tight_low && !tight_high {
                memo[i] = res;
            }
            res
        }

        dfs(0, true, true, lo, hi, limit, s, pre, n, &mut memo)
    }
}
```
