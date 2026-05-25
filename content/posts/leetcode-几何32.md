---
title: "leetcode-几何32"
date: 2026-05-11T20:20:56+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 统计梯形的数目 I

给你一个二维整数数组 points，其中 points[i] = [xi, yi] 表示第 i 个点在笛卡尔平面上的坐标。

水平梯形 是一种凸四边形，具有 至少一对 水平边（即平行于 x 轴的边）。两条直线平行当且仅当它们的斜率相同。

返回可以从 points 中任意选择四个不同点组成的 水平梯形 数量。

由于答案可能非常大，请返回结果对 109 + 7 取余数后的值。

```
use std::collections::HashMap;

impl Solution {
    /// 计算所有平行于 x 轴的线段（即相同 y 坐标的点对）的组合乘积之和。
    /// 等价于：对于所有不同的水平线，设第 i 条线上有 n_i 个点，
    /// 计算 sum_{i<j} C(n_i,2) * C(n_j,2) 模 1e9+7。
    /// 使用前缀和技巧线性扫描。
    pub fn count_trapezoids(points: Vec<Vec<i32>>) -> i32 {
        const MOD: i64 = 1_000_000_007;

        // 函数式风格：统计每个 y 坐标的点数
        let counts = points
            .into_iter()
            .fold(HashMap::<i32, i64>::new(), |mut map, p| {
                *map.entry(p[1]).or_insert(0) += 1;
                map
            });

        // 将所有点对数量 C(n,2) 收集为向量
        let edges = counts
            .into_values()
            .map(|n| n * (n - 1) / 2 % MOD);

        // 前缀累积：ans = sum_{i} edge_i * sum_{j<i} edge_j
        let (ans, _) = edges.fold((0, 0), |(ans, sum), edge| {
            let new_ans = (ans + edge * sum) % MOD;
            let new_sum = (sum + edge) % MOD;
            (new_ans, new_sum)
        });

        ans as i32
    }
}
```
