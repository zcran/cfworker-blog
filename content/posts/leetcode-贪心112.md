---
title: "leetcode-贪心112"
date: 2026-10-03T12:55:41+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 替换一个数字后的最大差值

给你一个整数 num 。你知道 Danny Mittal 会偷偷将 0 到 9 中的一个数字 替换 成另一个数字。

请你返回将 num 中 恰好一个 数字进行替换后，得到的最大值和最小值的差为多少。

注意：

当 Danny 将一个数字 d1 替换成另一个数字 d2 时，Danny 需要将 num 中所有 d1 都替换成 d2 。
Danny 可以将一个数字替换成它自己，也就是说 num 可以不变。
Danny 可以将数字分别替换成两个不同的数字分别得到最大值和最小值。
替换后得到的数字可以包含前导 0 。
Danny Mittal 获得周赛 326 前 10 名，让我们恭喜他。


```
impl Solution {
    pub fn min_max_difference(num: i32) -> i32 {
        let s = num.to_string();

        // 最大值：把从左起第一个不是 '9' 的数字全部替换为 '9'
        let max = match s.chars().find(|&c| c != '9') {
            Some(ch) => s.replace(ch, "9"),
            None => s.clone(), // 全是 9，无法变得更大
        };

        // 最小值：把首位数字全部替换为 '0'，得到该位数下的最小数
        let min = s.replace(s.chars().next().unwrap(), "0");

        max.parse::<i32>().unwrap() - min.parse::<i32>().unwrap()
    }
}
```
