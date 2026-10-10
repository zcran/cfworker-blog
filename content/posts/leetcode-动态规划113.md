---
title: "leetcode-动态规划113"
date: 2026-10-05T20:40:01+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 统计整数数目

给你两个数字字符串 num1 和 num2 ，以及两个整数 max_sum 和 min_sum 。如果一个整数 x 满足以下条件，我们称它是一个好整数：

num1 <= x <= num2

min_sum <= digit_sum(x) <= max_sum.

请你返回好整数的数目。答案可能很大，请返回答案对 10^9 + 7 取余后的结果。

注意，digit_sum(x) 表示 x 各位数字之和。


```
const MOD: i32 = 1_000_000_007;

impl Solution {
    pub fn count(num1: String, num2: String, min_sum: i32, max_sum: i32) -> i32 {
        // 将 num1 左补零到与 num2 等长，方便逐位比较
        let n = num2.len();
        let num1 = "0".repeat(n - num1.len()) + &num1;
        let low = num1.as_bytes();
        let high = num2.as_bytes();

        // memo[i][sum]：在第 i 位、已累计 digit_sum = sum、且不受上下界限制时的方案数
        // 用 -1 表示未计算；sum 上限为 max_sum
        let mut memo = vec![vec![-1i32; max_sum as usize + 1]; n];

        // 数位 DP：tight_low / tight_high 分别表示是否贴紧 num1 / num2 的前缀
        fn dfs(
            i: usize, sum: i32, tight_low: bool, tight_high: bool,
            n: usize, low: &[u8], high: &[u8], min_sum: i32, max_sum: i32,
            memo: &mut Vec<Vec<i32>>,
        ) -> i32 {
            // 剪枝：digit_sum 已超过 max_sum，后续只会更大
            if sum > max_sum {
                return 0;
            }
            if i == n {
                return if sum >= min_sum { 1 } else { 0 };
            }

            // 仅在不受上下界限制时可复用记忆化结果
            if !tight_low && !tight_high {
                let cached = memo[i][sum as usize];
                if cached >= 0 {
                    return cached;
                }
            }

            let lo = if tight_low { low[i] - b'0' } else { 0 };
            let hi = if tight_high { high[i] - b'0' } else { 9 };

            let mut res = 0;
            for d in lo..=hi {
                res = (res + dfs(
                    i + 1,
                    sum + d as i32,
                    tight_low && d == lo,
                    tight_high && d == hi,
                    n, low, high, min_sum, max_sum, memo,
                )) % MOD;
            }

            if !tight_low && !tight_high {
                memo[i][sum as usize] = res;
            }
            res
        }

        dfs(0, 0, true, true, n, low, high, min_sum, max_sum, &mut memo)
    }
}
```
