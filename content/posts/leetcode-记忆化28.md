---
title: "leetcode-记忆化28"
date: 2026-05-06T20:22:34+08:00
tags: ["leetcode", "记忆化"]
draft: false
---


## 不同骰子序列的数目

给你一个整数 n 。你需要掷一个 6 面的骰子 n 次。请你在满足以下要求的前提下，求出 不同 骰子序列的数目：

序列中任意 相邻 数字的 最大公约数 为 1 。
序列中 相等 的值之间，至少有 2 个其他值的数字。正式地，如果第 i 次掷骰子的值 等于 第 j 次的值，那么 abs(i - j) > 2 。
请你返回不同序列的 总数目 。由于答案可能很大，请你将答案对 109 + 7 取余 后返回。

如果两个序列中至少有一个元素不同，那么它们被视为不同的序列。

```
use std::sync::OnceLock;

// 模块级静态变量，存储预计算的 DP 表（线程安全，仅初始化一次）
// dp[len][last][prev] 表示长度为 len 的序列，最后一个数字为 last，倒数第二个数字为 prev 的方案数
// 数字 1..6 映射为索引 0..5
static DP: OnceLock<Vec<[[i32; 6]; 6]>> = OnceLock::new();

impl Solution {
    pub fn distinct_sequences(n: i32) -> i32 {
        const MOD: i32 = 1_000_000_007;
        const MX: usize = 10_000;
        let n = n as usize;

        let dp = DP.get_or_init(|| {
            let mut f = vec![[[0; 6]; 6]; MX + 1];

            // 初始化长度为 2 的序列
            for last in 0..6 {
                for prev in 0..6 {
                    if last != prev && gcd(last as i32 + 1, prev as i32 + 1) == 1 {
                        f[2][last][prev] = 1;
                    }
                }
            }

            // 递推长度从 2 到 MX-1
            for len in 2..MX {
                for last in 0..6 {
                    for prev in 0..6 {
                        let cur = f[len][last][prev];
                        if cur == 0 {
                            continue;
                        }
                        // 尝试下一个数字 nxt（将成为新的最后一个）
                        for nxt in 0..6 {
                            // 条件1: nxt 与当前最后一个数字互质且不相等
                            if nxt != last && gcd(nxt as i32 + 1, last as i32 + 1) == 1 {
                                // 条件2: nxt 不能与倒数第二个数字相等（防止三元组形如 a,b,a）
                                if nxt != prev {
                                    f[len + 1][nxt][last] = (f[len + 1][nxt][last] + cur) % MOD;
                                }
                            }
                        }
                    }
                }
            }
            f
        });

        if n == 1 {
            return 6;
        }

        // 对长度为 n 的所有状态求和
        dp[n]
            .iter()
            .flat_map(|row| row.iter())
            .fold(0, |acc, &x| (acc + x) % MOD)
    }
}

/// 求两个正整数的最大公约数
fn gcd(a: i32, b: i32) -> i32 {
    let mut a = a;
    let mut b = b;
    while b != 0 {
        let t = b;
        b = a % b;
        a = t;
    }
    a
}
```
