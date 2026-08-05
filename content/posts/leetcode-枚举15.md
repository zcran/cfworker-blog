---
title: "leetcode-枚举15"
date: 2026-07-09T10:05:00+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 统计平方和三元组的数目

一个 平方和三元组 (a,b,c) 指的是满足 a2 + b2 = c2 的 整数 三元组 a，b 和 c 。

给你一个整数 n ，请你返回满足 1 <= a, b, c <= n 的 平方和三元组 的数目。


```
impl Solution {
    /// 统计满足 1 <= a, b, c <= n 且 a² + b² = c² 的三元组数量。
    ///
    /// 优化思路：
    /// 1. 枚举 a 和 b，利用对称性减少一半计算（a 和 b 可交换）
    /// 2. 只枚举 a <= b，结果乘以 2（a=b 时不需要乘 2）
    /// 3. 使用整数运算避免浮点数精度问题
    pub fn count_triples(n: i32) -> i32 {
        let n = n as usize;
        let mut count = 0;

        // 枚举 a 和 b，只考虑 a <= b 利用对称性
        for a in 1..=n {
            let a2 = a * a;
            for b in a..=n {
                let c2 = a2 + b * b;
                let c = (c2 as f64).sqrt() as usize;

                // 检查 c 是否为整数且不超过 n
                if c <= n && c * c == c2 {
                    // a == b 时只有一种排列，否则 a 和 b 可交换有两种排列
                    count += if a == b { 1 } else { 2 };
                }
            }
        }

        count as i32
    }
}
```
