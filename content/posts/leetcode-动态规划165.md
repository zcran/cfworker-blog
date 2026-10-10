---
title: "leetcode-动态规划165"
date: 2026-10-05T20:40:05+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 范围内总波动值 II

给你两个整数 num1 和 num2，表示一个 闭 区间 [num1, num2]。

一个数字的 波动值 定义为该数字中 峰 和 谷 的总数：

如果一个数位 严格大于 其两个相邻数位，则该数位为 峰。
如果一个数位 严格小于 其两个相邻数位，则该数位为 谷。
数字的第一个和最后一个数位 不能 是峰或谷。
任何少于 3 位的数字，其波动值均为 0。

返回范围 [num1, num2] 内所有数字的波动值之和。


```
impl Solution {
    pub fn total_waviness(num1: i64, num2: i64) -> i64 {
        // 计算 [0, num] 内所有数字的波动值总和
        fn solve(num: i64) -> i64 {
            // 少于 3 位的数字波动值为 0
            if num < 100 {
                return 0;
            }

            let digits: Vec<i32> = num
                .to_string()
                .bytes()
                .map(|b| (b - b'0') as i32)
                .collect();
            let n = digits.len();

            // memo_cnt[pos][prev][curr]：方案数；memo_sum[pos][prev][curr]：波动值总和
            // 仅在 !is_limit && !is_leading && prev >= 0 && curr >= 0 时缓存
            let mut memo_cnt = vec![vec![vec![-1i64; 10]; 10]; n];
            let mut memo_sum = vec![vec![vec![0i64; 10]; 10]; n];

            // 返回 (方案数, 波动值总和)
            #[allow(clippy::too_many_arguments)]
            fn dfs(
                pos: usize,
                prev: i32,
                curr: i32,
                is_limit: bool,
                is_leading: bool,
                digits: &[i32],
                memo_cnt: &mut [Vec<Vec<i64>>],
                memo_sum: &mut [Vec<Vec<i64>>],
            ) -> (i64, i64) {
                if pos == digits.len() {
                    return (1, 0);
                }

                let cacheable = !is_limit && !is_leading && prev >= 0 && curr >= 0;
                if cacheable {
                    let (p, c) = (prev as usize, curr as usize);
                    if memo_cnt[pos][p][c] != -1 {
                        return (memo_cnt[pos][p][c], memo_sum[pos][p][c]);
                    }
                }

                let up = if is_limit { digits[pos] } else { 9 };
                let mut cnt = 0i64;
                let mut sum = 0i64;

                for d in 0..=up {
                    let new_leading = is_leading && d == 0;
                    let new_curr = if new_leading { -1 } else { d };

                    let (sub_cnt, sub_sum) = dfs(
                        pos + 1,
                        curr,
                        new_curr,
                        is_limit && d == up,
                        new_leading,
                        digits,
                        memo_cnt,
                        memo_sum,
                    );

                    // 当前位 curr 在 prev 与 d 之间构成峰/谷
                    if !new_leading && prev >= 0 && curr >= 0 {
                        if (prev < curr && curr > d) || (prev > curr && curr < d) {
                            sum += sub_cnt;
                        }
                    }

                    cnt += sub_cnt;
                    sum += sub_sum;
                }

                if cacheable {
                    let (p, c) = (prev as usize, curr as usize);
                    memo_cnt[pos][p][c] = cnt;
                    memo_sum[pos][p][c] = sum;
                }

                (cnt, sum)
            }

            dfs(0, -1, -1, true, true, &digits, &mut memo_cnt, &mut memo_sum).1
        }

        solve(num2) - solve(num1 - 1)
    }
}
```
