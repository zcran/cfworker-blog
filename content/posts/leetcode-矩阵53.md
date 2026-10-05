---
title: "leetcode-矩阵53"
date: 2026-09-19T10:41:35+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 一最多的行

给你一个大小为 m x n 的二进制矩阵 mat ，请你找出包含最多 1 的行的下标（从 0 开始）以及这一行中 1 的数目。

如果有多行包含最多的 1 ，只需要选择 行下标最小 的那一行。

返回一个由行下标和该行中 1 的数量组成的数组。


```
impl Solution {
    /// 找出含 1 最多的行：返回 [行下标, 该行 1 的个数]。
    /// 并列时取下标最小——用严格大于 `>` 更新，天然保留首个最大值。
    pub fn row_and_maximum_ones(mat: Vec<Vec<i32>>) -> Vec<i32> {
        let mut best_row = 0;
        let mut best_cnt = 0;

        for (i, row) in mat.iter().enumerate() {
            let cnt: i32 = row.iter().sum();
            // 严格大于才更新，保证并列时保留下标最小的行
            if cnt > best_cnt {
                best_cnt = cnt;
                best_row = i as i32;
            }
        }

        vec![best_row, best_cnt]
    }
}
```
