---
title: "leetcode-组合数学17"
date: 2026-05-24T09:54:13+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 恰好移动 k 步到达某一位置的方法数目

给你两个 正 整数 startPos 和 endPos 。最初，你站在 无限 数轴上位置 startPos 处。在一步移动中，你可以向左或者向右移动一个位置。

给你一个正整数 k ，返回从 startPos 出发、恰好 移动 k 步并到达 endPos 的 不同 方法数目。由于答案可能会很大，返回对 10^9 + 7 取余 的结果。

如果所执行移动的顺序不完全相同，则认为两种方法不同。

注意：数轴包含负整数。


```
impl Solution {
    const MOD: i64 = 1_000_000_007;

    /// 快速幂：计算 x^n mod MOD
    fn pow_mod(mut x: i64, mut n: i64) -> i64 {
        let mut res = 1;
        while n > 0 {
            if n & 1 == 1 {
                res = res * x % Self::MOD;
            }
            x = x * x % Self::MOD;
            n >>= 1;
        }
        res
    }

    /// 计算组合数 C(n, k) mod MOD，使用乘法逆元
    fn comb(n: i64, k: i64) -> i64 {
        if k < 0 || k > n {
            return 0;
        }
        let k = k.min(n - k); // 取较小的一半，减少循环次数
        let mut res = 1;
        for i in 1..=k {
            // 分子乘 (n - k + i)，分母除 i
            res = res * ((n - k + i) % Self::MOD) % Self::MOD;
            res = res * Self::pow_mod(i, Self::MOD - 2) % Self::MOD;
        }
        res
    }

    pub fn number_of_ways(start_pos: i32, end_pos: i32, k: i32) -> i32 {
        let diff = (start_pos - end_pos).abs() as i64;
        let k = k as i64;

        // 无法到达的情况：步数不够，或剩余步数为奇数
        if diff > k || (k - diff) % 2 != 0 {
            return 0;
        }

        let right = (k + diff) / 2; // 向右走的步数（即选择“向右”的次数）
        // 方案数 = C(k, right)
        Self::comb(k, right) as i32
    }
}
```
