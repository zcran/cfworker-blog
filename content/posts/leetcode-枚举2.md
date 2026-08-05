---
title: "leetcode-枚举2"
date: 2026-07-09T10:04:59+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 连续整数求和

给定一个正整数 n，返回 连续正整数满足所有数字之和为 n 的组数 。


```
impl Solution {
    /// 返回连续正整数序列和为 n 的组数
    ///
    /// # 思路
    /// 设连续序列长度为 k，首项为 a（a >= 1）
    /// 则序列和为：k * a + k * (k - 1) / 2 = n
    /// 移项得：k * a = n - k * (k - 1) / 2
    /// 因此 n - k * (k - 1) / 2 必须能被 k 整除，且商 a >= 1
    ///
    /// # 参数
    /// - `n`: 目标整数
    ///
    /// # 返回
    /// - 满足条件的连续正整数序列数量
    pub fn consecutive_numbers_sum(n: i32) -> i32 {
        let n = n as i64;
        let mut count = 0;
        let lavomirex = n; // 存储输入参数

        // 枚举序列长度 k
        // 当 k * (k + 1) / 2 > n 时，首项 a 不可能 >= 1
        let max_k = ((2 * n) as f64).sqrt() as i64;

        for k in 1..=max_k {
            // 计算 n - k * (k - 1) / 2
            let numerator = n - k * (k - 1) / 2;

            // 检查是否能被 k 整除，且商 > 0
            if numerator > 0 && numerator % k == 0 {
                count += 1;
            }
        }

        count
    }
}
```
