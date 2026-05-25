---
title: "leetcode-记忆化16"
date: 2026-05-06T20:22:33+08:00
tags: ["leetcode", "记忆化"]
draft: false
---


## 将整数按权重排序

我们将整数 x 的 权重 定义为按照下述规则将 x 变成 1 所需要的步数：

如果 x 是偶数，那么 x = x / 2
如果 x 是奇数，那么 x = 3 * x + 1

比方说，x=3 的权重为 7 。因为 3 需要 7 步变成 1 （3 --> 10 --> 5 --> 16 --> 8 --> 4 --> 2 --> 1）。

给你三个整数 lo， hi 和 k 。你的任务是将区间 [lo, hi] 之间的整数按照它们的权重 升序排序 ，如果大于等于 2 个整数有 相同 的权重，那么按照数字自身的数值 升序排序 。

请你返回区间 [lo, hi] 之间的整数按权重排序后的第 k 个数。

注意，题目保证对于任意整数 x （lo <= x <= hi） ，它变成 1 所需要的步数是一个 32 位有符号整数。


```
use std::collections::HashMap;

impl Solution {
    /// 返回区间 [lo, hi] 中，按 Collatz 步数升序（步数相同则按数值升序）排列后的第 k 个数。
    /// 步数定义为将数字通过 Collatz 变换（奇数 → 3x+1，偶数 → x/2）变为 1 所需次数。
    pub fn get_kth(lo: i32, hi: i32, k: i32) -> i32 {
        // 缓存已计算过的步数，避免重复递归
        let mut cache = HashMap::new();

        // 构造区间内所有数字，并生成 (数值, 步数) 对
        let mut pairs: Vec<(i32, i32)> = (lo..=hi)
            .map(|num| (num, Self::collatz_steps(num, &mut cache)))
            .collect();

        // 按步数升序，步数相同按数值升序排序（函数式风格）
        pairs.sort_by(|a, b| a.1.cmp(&b.1).then(a.0.cmp(&b.0)));

        // 返回第 k 个数值（k 从 1 开始计数）
        pairs[k as usize - 1].0
    }

    /// 计算数字 x 的 Collatz 步数（记忆化递归）
    fn collatz_steps(x: i32, cache: &mut HashMap<i32, i32>) -> i32 {
        if x == 1 {
            return 0;
        }
        // 如果已缓存，直接返回
        if let Some(&steps) = cache.get(&x) {
            return steps;
        }

        // 递归计算下一步的步数，并加 1
        let next_steps = if x % 2 == 1 {
            Self::collatz_steps(x * 3 + 1, cache)
        } else {
            Self::collatz_steps(x / 2, cache)
        } + 1;

        // 存入缓存并返回
        cache.insert(x, next_steps);
        next_steps
    }
}
```
