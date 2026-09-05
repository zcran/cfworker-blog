---
title: "leetcode-堆54"
date: 2026-08-21T09:54:14+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 将珠子放入背包中

你有 k 个背包。给你一个下标从 0 开始的整数数组 weights ，其中 weights[i] 是第 i 个珠子的重量。同时给你整数 k 。

请你按照如下规则将所有的珠子放进 k 个背包。

没有背包是空的。
如果第 i 个珠子和第 j 个珠子在同一个背包里，那么下标在 i 到 j 之间的所有珠子都必须在这同一个背包中。
如果一个背包有下标从 i 到 j 的所有珠子，那么这个背包的价格是 weights[i] + weights[j] 。

一个珠子分配方案的 分数 是所有 k 个背包的价格之和。

请你返回所有分配方案中，最大分数 与 最小分数 的 差值 为多少。


```
impl Solution {
    pub fn put_marbles(weights: Vec<i32>, k: i32) -> i64 {
        let k = k as usize;
        let n = weights.len();
        if k == 1 || n == 1 {
            return 0;
        }

        // 计算所有相邻对的和：weights[i] + weights[i+1]
        // 这些对代表在位置 i 和 i+1 之间切分的"代价"
        let mut pairs: Vec<i64> = weights
            .windows(2)
            .map(|w| (w[0] + w[1]) as i64)
            .collect();

        // 排序后取最大 k-1 个和最小 k-1 个的差
        pairs.sort_unstable();

        let mut ans = 0i64;
        let m = pairs.len();
        for i in 0..k - 1 {
            ans += pairs[m - 1 - i] - pairs[i];
        }

        ans
    }
}
```
