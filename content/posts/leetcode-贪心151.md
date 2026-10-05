---
title: "leetcode-贪心151"
date: 2026-10-03T12:55:44+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 吃披萨

给你一个长度为 n 的整数数组 pizzas，其中 pizzas[i] 表示第 i 个披萨的重量。每天你会吃 恰好 4 个披萨。由于你的新陈代谢能力惊人，当你吃重量为 W、X、Y 和 Z 的披萨（其中 W <= X <= Y <= Z）时，你只会增加 1 个披萨的重量！体重增加规则如下：

在 奇数天（按 1 开始计数）你会增加 Z 的重量。
在 偶数天，你会增加 Y 的重量。

请你设计吃掉 所有 披萨的最优方案，并计算你可以增加的 最大 总重量。

注意：保证 n 是 4 的倍数，并且每个披萨只吃一次。


```
impl Solution {
    pub fn max_weight(mut pizzas: Vec<i32>) -> i64 {
        // 贪心：将披萨按重量降序排序。
        // 每天吃 4 个，奇数天取当天最大（Z），偶数天取次大（Y）。
        // 奇数天比偶数天多一天（或相等），优先把最重的分给奇数天。
        // 奇数天取前 odd 个最重的；偶数天从剩余中每隔一个取一个（跳过最小的那个）。
        pizzas.sort_unstable_by(|a, b| b.cmp(a));

        let days = pizzas.len() / 4;
        let odd = (days + 1) / 2;

        let mut ans = 0i64;
        // 奇数天：取最重的 odd 个
        for i in 0..odd {
            ans += pizzas[i] as i64;
        }
        // 偶数天：在剩余中每隔一个取一个（作为每天的 Y）
        for i in 0..days / 2 {
            ans += pizzas[odd + i * 2 + 1] as i64;
        }
        ans
    }
}
```
