---
title: "leetcode-矩阵57"
date: 2026-09-19T10:41:35+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 找到矩阵中的好子集

给你一个下标从 0 开始大小为 m x n 的二进制矩阵 grid 。

从原矩阵中选出若干行构成一个行的 非空 子集，如果子集中任何一列的和至多为子集大小的一半，那么我们称这个子集是 好子集。

更正式的，如果选出来的行子集大小（即行的数量）为 k，那么每一列的和至多为 floor(k / 2) 。

请你返回一个整数数组，它包含好子集的行下标，请你将其 升序 返回。

如果有多个好子集，你可以返回任意一个。如果没有好子集，请你返回一个空数组。

一个矩阵 grid 的行 子集 ，是删除 grid 中某些（也可能不删除）行后，剩余行构成的元素集合。


```
impl Solution {
    /// 好子集判定 + 关键观察：
    ///   1. 若存在全 0 行（位掩码 0），单独选它即可：k=1 时每列和 0 ≤ floor(1/2)=0，返回该行。
    ///   2. 否则好子集大小最多为 2。因为每列至多允许 floor(k/2) 个 1；
    ///      若 k≥3，任意两行按列"与"必须为 0，而这在无零行时不可能，故只需检查两行。
    ///   所以答案要么是单行（全 0），要么是两行按位与为 0。
    ///
    /// 用位掩码压缩每行，值域 0..2^n（n ≤ 5，≤32 种），用 HashMap 去重：
    ///   同一掩码只需保留一个行下标（重复掩码对"找两行与为 0"无额外价值）。
    ///
    /// 时间复杂度 O(m*n + 4^n)，空间 O(2^n)。
    pub fn good_subsetof_binary_matrix(grid: Vec<Vec<i32>>) -> Vec<i32> {
        let n = grid[0].len();

        // 每种行掩码只保留一个行下标（去重）
        let mut mask_to_row: std::collections::HashMap<i32, i32> =
            std::collections::HashMap::new();
        for (j, row) in grid.iter().enumerate() {
            let mut mask = 0i32;
            for (i, &v) in row.iter().enumerate() {
                mask |= v << i;
            }
            mask_to_row.entry(mask).or_insert(j as i32);
        }

        // 情况 1：存在全 0 行
        if let Some(&r) = mask_to_row.get(&0) {
            return vec![r];
        }

        // 情况 2：找两行掩码按位与为 0
        let entries: Vec<(i32, i32)> = mask_to_row.into_iter().collect();
        for a in 0..entries.len() {
            for b in a + 1..entries.len() {
                if entries[a].0 & entries[b].0 == 0 {
                    let (i, j) = (entries[a].1, entries[b].1);
                    return vec![i.min(j), i.max(j)];
                }
            }
        }

        vec![]
    }
}
```
