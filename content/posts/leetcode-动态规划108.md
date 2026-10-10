---
title: "leetcode-动态规划108"
date: 2026-10-05T20:40:01+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 统计构造好字符串的方案数

给你整数 zero ，one ，low 和 high ，我们从空字符串开始构造一个字符串，每一步执行下面操作中的一种：

将 '0' 在字符串末尾添加 zero  次。
将 '1' 在字符串末尾添加 one 次。

以上操作可以执行任意次。

如果通过以上过程得到一个 长度 在 low 和 high 之间（包含上下边界）的字符串，那么这个字符串我们称为 好 字符串。

请你返回满足以上要求的 不同 好字符串数目。由于答案可能很大，请将结果对 10^9 + 7 取余 后返回。


```
const MOD: i32 = 1_000_000_007;

impl Solution {
    pub fn count_good_strings(low: i32, high: i32, zero: i32, one: i32) -> i32 {
        // 所有可构造长度都是 gcd(zero, one) 的倍数，先约分缩小范围
        let g = Self::gcd(zero, one);
        let low = (low - 1) / g + 1; // 向上取整到 ≥ low 的最小倍数
        let high = high / g;
        let zero = (zero / g) as usize;
        let one = (one / g) as usize;

        // f[i]: 构造长度为 i 的字符串的方案数
        let mut f = vec![0i32; high as usize + 1];
        f[0] = 1;

        let mut ans = 0i32;
        for i in 1..=high as usize {
            let mut ways = 0i32;
            if i >= zero {
                ways = f[i - zero];
            }
            if i >= one {
                ways = (ways + f[i - one]) % MOD;
            }
            f[i] = ways;
            if i >= low as usize {
                ans = (ans + ways) % MOD;
            }
        }

        ans
    }

    fn gcd(a: i32, b: i32) -> i32 {
        if b == 0 { a } else { Self::gcd(b, a % b) }
    }
}
```
