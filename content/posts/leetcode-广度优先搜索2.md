---
title: "leetcode-广度优先搜索2"
date: 2026-09-13T09:32:06+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 完全平方数

给你一个整数 n ，返回 和为 n 的完全平方数的最少数量 。

完全平方数 是一个整数，其值等于另一个整数的平方；换句话说，其值等于一个整数自乘的积。例如，1、4、9 和 16 都是完全平方数，而 3 和 11 不是。


```
impl Solution {
    pub fn num_squares(n: i32) -> i32 {
        let n = n as usize;

        // dp[j] = 凑出 j 所需的最少完全平方数个数
        let mut dp = vec![i32::MAX; n + 1];
        dp[0] = 0;

        // 预处理所有 <= n 的完全平方数
        let squares: Vec<usize> = (1..)
            .map(|i| i * i)
            .take_while(|&s| s <= n)
            .collect();

        // 完全背包：每个平方数可重复使用
        for &sq in &squares {
            for j in sq..=n {
                if dp[j - sq] != i32::MAX {
                    dp[j] = dp[j].min(dp[j - sq] + 1);
                }
            }
        }

        dp[n]
    }
}
```
