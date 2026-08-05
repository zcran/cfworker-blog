---
title: "leetcode-滑动窗口34"
date: 2026-07-18T11:02:31+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 可获得的最大点数

几张卡牌 排成一行，每张卡牌都有一个对应的点数。点数由整数数组 cardPoints 给出。

每次行动，你可以从行的开头或者末尾拿一张卡牌，最终你必须正好拿 k 张卡牌。

你的点数就是你拿到手中的所有卡牌的点数之和。

给你一个整数数组 cardPoints 和整数 k，请你返回可以获得的最大点数。


```
impl Solution {
    pub fn max_score(card_points: Vec<i32>, k: i32) -> i32 {
        let n = card_points.len();
        let window_size = n - k as usize; // 需要跳过的中间连续子数组长度
        let total: i32 = card_points.iter().sum();

        // 如果 k 等于数组长度，直接返回总和
        if window_size == 0 {
            return total;
        }

        // 找长度为 window_size 的连续子数组的最小和
        let mut window_sum: i32 = card_points[..window_size].iter().sum();
        let mut min_window_sum = window_sum;

        for i in window_size..n {
            window_sum += card_points[i] - card_points[i - window_size];
            min_window_sum = min_window_sum.min(window_sum);
        }

        total - min_window_sum
    }
}
```
