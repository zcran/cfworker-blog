---
title: "leetcode-动态规划173"
date: 2026-10-05T20:40:06+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 统计范围内的好整数

给你三个整数 l，r 和 k。

如果一个数字中每一对 相邻 数位之间的 绝对差 都 至多 为 k，则称该数字为 好数。

返回在范围 [l, r]（包含边界）内的 好 整数的数量。

值 x 和 y 之间的 绝对差 定义为 abs(x - y)。


```
impl Solution {
    pub fn good_integers(l: i64, r: i64, k: i32) -> i64 {
        let low_s: Vec<i32> = l.to_string().bytes().map(|b| (b - b'0') as i32).collect();
        let high_s: Vec<i32> = r.to_string().bytes().map(|b| (b - b'0') as i32).collect();
        let n = high_s.len();
        let diff_lh = n - low_s.len();

        // memo[i][pre] 保存 res + 1，这样初值 0 表示未计算
        let mut memo = vec![[0i64; 10]; n];

        fn dfs(
            i: usize,
            pre: usize,
            limit_low: bool,
            limit_high: bool,
            n: usize,
            diff_lh: usize,
            k: i32,
            low_s: &[i32],
            high_s: &[i32],
            memo: &mut [[i64; 10]],
        ) -> i64 {
            if i == n {
                return 1; // 找到一个好数
            }

            if !limit_low && !limit_high && memo[i][pre] > 0 {
                return memo[i][pre] - 1;
            }

            let lo = if limit_low && i >= diff_lh {
                low_s[i - diff_lh]
            } else {
                0
            };
            let hi = if limit_high { high_s[i] } else { 9 };

            let mut res = 0i64;
            let mut d = lo;

            // 前导零区间：可以不填数字，上界不受约束
            if limit_low && i < diff_lh {
                res = dfs(i + 1, 0, true, false, n, diff_lh, k, low_s, high_s, memo);
                d = 1; // 正式填数字时从 1 开始，避免前导零
            }

            // 若 diff_lh 之前未填数字，则当前位是首位，不受相邻差约束
            let is_first = limit_low && i <= diff_lh;
            while d <= hi {
                if is_first || (d - pre as i32).abs() <= k {
                    res += dfs(
                        i + 1,
                        d as usize,
                        limit_low && d == lo,
                        limit_high && d == hi,
                        n,
                        diff_lh,
                        k,
                        low_s,
                        high_s,
                        memo,
                    );
                }
                d += 1;
            }

            if !limit_low && !limit_high {
                memo[i][pre] = res + 1;
            }
            res
        }

        dfs(0, 0, true, true, n, diff_lh, k, &low_s, &high_s, &mut memo)
    }
}
```
