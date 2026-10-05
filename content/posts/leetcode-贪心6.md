---
title: "leetcode-贪心6"
date: 2026-10-03T12:55:34+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 分发糖果

n 个孩子站成一排。

给你一个整数数组 ratings 表示每个孩子的评分。

你需要按照以下要求，给这些孩子分发糖果：

每个孩子 至少 分配到 1 个糖果。
相邻两个孩子中，评分 更高 的那个会获得更多的糖果。

请你给每个孩子分发糖果，计算并返回需要准备的 最少 糖果数目。


```
impl Solution {
    pub fn candy(ratings: Vec<i32>) -> i32 {
        let n = ratings.len();
        // 先给每个孩子 1 颗糖
        let mut candies = vec![1; n];

        // 从左到右：保证右边评分更高的孩子糖果更多
        for i in 1..n {
            if ratings[i] > ratings[i - 1] {
                candies[i] = candies[i - 1] + 1;
            }
        }

        // 从右到左：保证左边评分更高的孩子糖果更多
        for i in (0..n - 1).rev() {
            if ratings[i] > ratings[i + 1] {
                candies[i] = candies[i].max(candies[i + 1] + 1);
            }
        }

        candies.iter().sum()
    }
}
```
