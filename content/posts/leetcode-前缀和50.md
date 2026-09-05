---
title: "leetcode-前缀和50"
date: 2026-08-25T10:02:49+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 划分数字的方案数

你写下了若干 正整数 ，并将它们连接成了一个字符串 num 。但是你忘记给这些数字之间加逗号了。你只记得这一列数字是 非递减 的且 没有 任何数字有前导 0 。

请你返回有多少种可能的 正整数数组 可以得到字符串 num 。由于答案可能很大，将结果对 10^9 + 7 取余 后返回。


```
impl Solution {
    pub fn number_of_combinations(num: String) -> i32 {
        // 如果第一个字符是 '0'，任何数字都不能有前导0，因此无解
        if num.chars().nth(0) == Some('0') {
            return 0;
        }

        let n = num.len();
        const MOD: i64 = 1_000_000_007;

        // ord[i][len] 表示从位置 i 开始，长度为 len 的子串的排名
        // 用于比较任意两个子串的字典序（数值大小）
        let mut ord = vec![vec![0; n + 1]; n];

        // 初始化长度为1的子串排名为对应的数字
        for i in 0..n {
            ord[i][1] = (num.chars().nth(i).unwrap() as u8 - b'0') as usize;
        }

        let mut nr_ord = 10; // 当前不同排名的数量

        // 倍增法计算所有长度子串的排名
        for len in 2..n {
            // next_ord 用于计算长度为 len 的新排名
            // 每个子串可以用 (前半部分排名, 后半部分排名) 唯一标识
            let mut next_ord = vec![0; nr_ord * 10];

            // 标记所有存在的二元组
            for i in 0..=n - len {
                let first = ord[i][len - 1];
                let second = ord[i + len - 1][1];
                next_ord[first * 10 + second] = 1;
            }

            // 将标记的二元组映射为连续的排名
            let mut next_nr_ord = 0;
            for i in 0..nr_ord * 10 {
                if next_ord[i] == 1 {
                    next_ord[i] = next_nr_ord;
                    next_nr_ord += 1;
                }
            }

            // 更新所有子串的排名
            for i in 0..=n - len {
                let first = ord[i][len - 1];
                let second = ord[i + len - 1][1];
                ord[i][len] = next_ord[first * 10 + second];
            }

            nr_ord = next_nr_ord;
        }

        // dp[l][k] 表示处理到位置 l（包含），最后一段长度为 k 的方案数
        // 其中 dp[l][k] 具体含义：将 num[0..=l] 划分为若干个非递减数字，
        // 且最后一段为 num[l-k+1..=l]（长度为 k）的方案数
        // 额外使用 dp[l][l+2] 存储所有可行方案的总和
        let mut dp = vec![vec![0; n + 2]; n];

        for l in 0..n {
            let mut sum = 0_i64; // 累计所有合法方案数

            // k 表示最后一段的长度
            for k in 1..=l + 1 {
                // 当前 dp[l][k] 暂时存储"前一段长度小于 k"的方案数
                dp[l][k] = sum;

                let start = l + 1 - k; // 当前段的起始位置

                // 检查当前段是否有前导0（即起始字符是否为 '0'）
                if ord[start][1] != 0 {
                    // 情况1：整个前缀作为一段（k == l+1）
                    if k == l + 1 {
                        sum = (sum + 1) % MOD;
                    } else {
                        // 情况2：前一段长度可以小于等于 k
                        // 需要累加 dp[l-k][x] 其中 x <= k 的所有方案
                        let prev_l = l - k; // 前一段的结束位置
                        let prev_sum = if k > prev_l + 1 {
                            // 如果 k 大于前一段所有可能长度，取全部和
                            dp[prev_l][prev_l + 2]
                        } else {
                            // 否则取长度 <= k 的和
                            dp[prev_l][k]
                        };
                        sum = (sum + prev_sum) % MOD;
                    }

                    // 情况3：如果当前段长度 k 不超过剩余长度，且满足非递减条件
                    // 比较当前段和前一段（长度为 k）的大小
                    if l + k < n && ord[start][k] <= ord[l + 1][k] {
                        // 将 dp[l][k] 更新为方案数，而非之前的临时值
                        dp[l][k] = sum;
                    }
                }
            }

            // dp[l][l+2] 存储所有以位置 l 结尾的划分方案总数
            dp[l][l + 2] = sum;
        }

        // 返回整个字符串的划分方案数
        dp[n - 1][n + 1] as i32
    }
}
```
