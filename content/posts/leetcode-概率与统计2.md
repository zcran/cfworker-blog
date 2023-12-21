---
title: "Leetcode 概率与统计2"
date: 2023-12-19T15:47:54+08:00
tags: ["leetcode", "概率与统计"]
draft: false
---

## 分汤

有 A 和 B 两种类型 的汤。一开始每种类型的汤有 n 毫升。有四种分配操作：

提供 100ml 的 汤A 和 0ml 的 汤B 。
提供 75ml 的 汤A 和 25ml 的 汤B 。
提供 50ml 的 汤A 和 50ml 的 汤B 。
提供 25ml 的 汤A 和 75ml 的 汤B 。
当我们把汤分配给某人之后，汤就没有了。每个回合，我们将从四种概率同为 0.25 的操作中进行分配选择。如果汤的剩余量不足以完成某次操作，我们将尽可能分配。当两种类型的汤都分配完时，停止操作。

注意 不存在先分配 100 ml 汤B 的操作。

需要返回的值： 汤A 先分配完的概率 +  汤A和汤B 同时分配完的概率 / 2。返回值在正确答案 10-5 的范围内将被认为是正确的。

```
impl Solution {
    pub fn soup_servings(n: i32) -> f64 {
        fn recursive(dp: &mut Vec<Vec<f64>>, a: i32, b: i32) -> f64 {
            if a <= 0 && b <= 0 { return 0.5; }
            if a <= 0 { return 1.0; }
            if b <= 0 { return 0.0; }
            if dp[a as usize][b as usize] > 0.0 { return dp[a as usize][b as usize]; }
            dp[a as usize][b as usize] = 0.25 * (recursive(dp, a - 4, b) + recursive(dp, a - 3, b - 1) + recursive(dp, a - 2, b - 2) + recursive(dp, a - 1, b - 3));
            dp[a as usize][b as usize]
        }
        let mut dp = vec![vec![0.0; 200]; 200];
        if n > 4800 { 1.0 } else { recursive(&mut dp, (n + 24) / 25, (n + 24) / 25) }
    }
}
```