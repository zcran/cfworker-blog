---
title: "leetcode-枚举39"
date: 2026-07-09T10:05:01+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 收集巧克力

给你一个长度为 n、下标从 0 开始的整数数组 nums，nums[i] 表示收集位于下标 i 处的巧克力成本。每个巧克力都对应一个不同的类型，最初，位于下标 i 的巧克力就对应第 i 个类型。

在一步操作中，你可以用成本 x 执行下述行为：

同时修改所有巧克力的类型，将巧克力的类型 i^th 修改为类型 ((i + 1) mod n)^th。

假设你可以执行任意次操作，请返回收集所有类型巧克力所需的最小成本。


```
impl Solution {
    pub fn min_cost(nums: Vec<i32>, x: i32) -> i64 {
        let n = nums.len();
        let x = x as i64;

        // s[k] 表示执行 k 次操作后的总成本
        // 初始成本为：每个巧克力都通过操作获得，总成本 = k * x
        let mut cost = vec![0; n];
        for k in 0..n {
            cost[k] = k as i64 * x;
        }

        // 枚举每个起点 i，考虑从 i 开始的连续子数组
        for i in 0..n {
            let mut min_val = nums[i];
            // 从起点 i 出发，向右扩展 j 步（j = 0..n-1）
            // 访问 j 次后，当前位置为 (i+j) % n，相当于收集了 j+1 个巧克力
            for j in 0..n {
                let idx = (i + j) % n;
                min_val = min_val.min(nums[idx]);
                // 执行 j 次操作后，这 j+1 个巧克力中最小成本为 min_val
                cost[j] += min_val as i64;
            }
        }

        *cost.iter().min().unwrap()
    }
}
```
