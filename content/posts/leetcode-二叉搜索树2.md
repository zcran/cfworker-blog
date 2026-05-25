---
title: "leetcode-二叉搜索树2"
date: 2026-05-14T20:16:14+08:00
tags: ["leetcode", "二叉搜索树"]
draft: false
---


## 不同的二叉搜索树


给你一个整数 n ，求恰由 n 个节点组成且节点值从 1 到 n 互不相同的 二叉搜索树 有多少种？返回满足题意的二叉搜索树的种数。

```
impl Solution {
    /// 计算第 n 个卡特兰数（n 个节点的不同 BST 数量）
    /// 递推公式：C₀ = 1, Cₙ = Cₙ₋₁ * 2*(2n-1) / (n+1)
    pub fn num_trees(n: i32) -> i32 {
        let n = n as usize;
        // 函数式风格：使用 fold 累积计算卡特兰数，全程 i64 避免溢出
        (1..=n)
            .fold(1_i64, |c, i| c * 2 * (2 * i - 1) as i64 / (i + 1) as i64)
            as i32
    }
}
```
