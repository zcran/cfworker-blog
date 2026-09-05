---
title: "leetcode-堆1"
date: 2026-08-21T09:54:10+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 丑数 II

给你一个整数 n ，请你找出并返回第 n 个 丑数 。

丑数 就是质因子只包含 2、3 和 5 的正整数。


```
impl Solution {
    /// 三指针动态规划求第 n 个丑数
    ///
    /// 丑数：质因子仅含 2、3、5 的正整数。
    /// 核心思想：每个丑数都可以由前面某个丑数 ×2/×3/×5 得到。
    /// 用三个指针分别跟踪下一个待乘 2/3/5 的位置，取最小值填入序列。
    pub fn nth_ugly_number(n: i32) -> i32 {
        let n = n as usize;
        let mut dp = vec![0; n];
        dp[0] = 1;

        let (mut i2, mut i3, mut i5) = (0, 0, 0);

        for i in 1..n {
            let next = (dp[i2] * 2).min(dp[i3] * 3).min(dp[i5] * 5);
            dp[i] = next;
            // 多个指针可能指向相同值（如 6 = 2×3 = 3×2），需全部前移
            if next == dp[i2] * 2 { i2 += 1; }
            if next == dp[i3] * 3 { i3 += 1; }
            if next == dp[i5] * 5 { i5 += 1; }
        }

        dp[n - 1]
    }
}
```
