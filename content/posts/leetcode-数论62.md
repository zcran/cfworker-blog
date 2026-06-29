---
title: "leetcode-数论62"
date: 2026-06-20T11:09:57+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 完全质数

给你一个整数 num。

如果一个数 num 的每一个 前缀 和每一个 后缀 都是 质数，则称该数为 完全质数。

如果 num 是完全质数，返回 true，否则返回 false。

注意：

· 一个数的 前缀 是由该数的 前 k 位数字构成的。
· 一个数的 后缀 是由该数的 后 k 位数字构成的。
· 质数 是大于 1 且只有两个因子（1 和它本身）的自然数。
· 个位数只有在它是 质数 时才被视为完全质数。



```
impl Solution {
    /// 判断一个数是否是完全质数（所有前缀和后缀都是质数）
    pub fn complete_prime(num: i32) -> bool {
        // 质数检测：n 必须 > 1，且不能被 2..sqrt(n) 整除
        fn is_prime(n: i32) -> bool {
            if n < 2 { return false; }
            if n % 2 == 0 { return n == 2; } // 快速排除偶数
            let mut d = 3;
            while d * d <= n {
                if n % d == 0 { return false; }
                d += 2; // 只检查奇数因子
            }
            true
        }

        let s = num.to_string();
        let digits = s.as_bytes();
        let len = s.len();

        // 计算所有前缀和后缀的数值
        let mut prefix = 0;
        let mut suffix_pow = 1;
        let mut suffix = num; // 从完整数字开始逐步取后缀

        // 预计算 10^(len-1) 用于取后缀
        for _ in 1..len {
            suffix_pow *= 10;
        }

        for i in 0..len {
            // 计算前缀：逐步添加下一位数字
            prefix = prefix * 10 + (digits[i] - b'0') as i32;
            if !is_prime(prefix) { return false; }

            // 计算后缀：去掉最高位
            // 每次去掉 suffix 的最高位
            if i > 0 {
                suffix %= suffix_pow;
                suffix_pow /= 10;
            }
            if !is_prime(suffix) { return false; }
        }

        true
    }
}
```
