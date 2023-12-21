---
title: "Leetcode 后缀数组2"
date: 2023-12-19T15:46:47+08:00
tags: ["leetcode", "后缀数组"]
draft: false
---

## 划分数字的方案数

你写下了若干 正整数 ，并将它们连接成了一个字符串 num 。但是你忘记给这些数字之间加逗号了。你只记得这一列数字是 非递减 的且 没有 任何数字有前导 0 。

请你返回有多少种可能的 正整数数组 可以得到字符串 num 。由于答案可能很大，将结果对 10^9 + 7 取余 后返回。

```
impl Solution {
    pub fn number_of_combinations(num: String) -> i32 {
        if num.chars().nth(0) == Some('0') {return 0;}
        let n = num.len();
        let mut ord = vec![vec![0;n + 1];n];
        for i in 0..n {
            ord[i][1] = (num.chars().nth(i).unwrap() as u8 - '0' as u8) as usize;
        }
        let mut nr_ord = 10;
        for l in 2..n {
            let mut next_ord = vec![0;nr_ord * 10];
            for i in 0..=n - l {
                next_ord[ord[i][l - 1] * 10 + ord[i + l - 1][1]] = 1;
            }
            let mut next_nr_ord = 0;
            for i in 0..nr_ord * 10 {
                if (next_ord[i] == 1) {
                    next_ord[i] = next_nr_ord;
                    next_nr_ord += 1;
                }
            }
            for i in 0..=n - l {
                ord[i][l] = next_ord[ord[i][l - 1] * 10 + ord[i + l - 1][1]];
            }
            nr_ord = next_nr_ord;
        }
        let mut dp = vec![vec![0;n + 2];n];
        for l in 0..n {
            let mut sum = 0_i64;
            for k in 1..=l + 1{
                dp[l][k] = sum;
                if ord[l + 1 - k][1] != 0 {
                    if k == l + 1 {
                        sum = (sum + 1) % 1000000007;
                    } else {
                        sum = (sum + if k > l + 1 - k {dp[l - k][l - k + 2]} else {dp[l - k][k]}) % 1000000007;
                    }
                    if l + k < n && ord[l + 1 - k][k] <= ord[l + 1][k] {
                        dp[l][k] = sum;
                    }
                }
            }
            dp[l][l + 2] = sum;
        }
        dp[n - 1][n + 1] as i32
    }
}
```