---
title: "leetcode-贪心181"
date: 2026-10-03T12:55:46+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 给定数位和的最大整数

给你两个非负整数 n 和 s。

返回满足下述条件的 最大 整数：

最多有 n 位数字。

其各位数字之和等于 s 。

如果不存在这样的整数，则返回 -1。


```
impl Solution {
    pub fn largest_integer(n: i32, s: i32) -> i32 {
        // 最多 n 位，数字之和为 s，要使数值最大：
        // 位数尽量多、且高位尽量大。但位数不超过 n，也不超过 s（每位数至少 0，但首位需 >0）。
        // 直接贪心：尽量让高位为 9，多余的位补 0。
        if s > n * 9 {
            return -1;
        }

        // 若 s == 0，只有 1 位数时合法（数值 0），否则没有合法整数
        if s == 0 {
            return if n >= 1 { 0 } else { -1 };
        }

        // 先构造由 s 决定的数字：k = s/9 个 9，若有余数 rem 则紧跟一个 rem
        // 然后整体左移若干位（末尾补 0）以填满 n 位（但不足 n 位时保持原样）
        let k = s / 9;
        let rem = s % 9;

        let mut digits = Vec::new();
        for _ in 0..k {
            digits.push(9);
        }
        if rem > 0 {
            digits.push(rem);
        }
        // 剩余补 0 的位数 = n - digits.len()（若为负说明 s 用更少位就能表示，无需补）
        while (digits.len() as i32) < n {
            digits.push(0);
        }

        // 拼接成整数（注意可能溢出 i32，题目保证结果在范围内则无妨）
        let mut ans = 0i32;
        for d in digits {
            ans = ans * 10 + d;
        }
        ans
    }
}
```
