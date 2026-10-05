---
title: "leetcode-贪心138"
date: 2026-10-03T12:55:43+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 幸福值最大化的选择方案

给你一个长度为 n 的数组 happiness ，以及一个 正整数 k 。

n 个孩子站成一队，其中第 i 个孩子的 幸福值 是 happiness[i] 。你计划组织 k 轮筛选从这 n 个孩子中选出 k 个孩子。

在每一轮选择一个孩子时，所有 尚未 被选中的孩子的 幸福值 将减少 1 。注意，幸福值 不能 变成负数，且只有在它是正数的情况下才会减少。

选择 k 个孩子，并使你选中的孩子幸福值之和最大，返回你能够得到的 最大值 。


```
impl Solution {
    pub fn maximum_happiness_sum(mut happiness: Vec<i32>, k: i32) -> i64 {
        // 贪心：幸福值高的孩子先选，避免被后续轮次的衰减拉低。
        // 第 i 轮（0-indexed）选中的孩子实际贡献为 max(happiness - i, 0)。
        happiness.sort_unstable_by(|a, b| b.cmp(a));

        happiness
            .iter()
            .take(k as usize)
            .enumerate()
            .map(|(i, &h)| (h as i64 - i as i64).max(0))
            .sum()
    }
}
```
