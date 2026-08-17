---
title: "leetcode-模拟73"
date: 2026-08-08T11:31:12+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 相等行列对

给你一个下标从 0 开始、大小为 n x n 的整数矩阵 grid ，返回满足 Ri 行和 Cj 列相等的行列对 (Ri, Cj) 的数目。

如果行和列以相同的顺序包含相同的元素（即相等的数组），则认为二者是相等的。


```
use std::collections::HashMap;

impl Solution {
    /// 统计与某行完全相同的列的数量。
    ///
    /// 思路：用 HashMap 统计每种行模式出现的次数，然后遍历每一列，
    /// 在 map 中查找匹配，累加计数。时间 O(n²)，空间 O(n²)。
    pub fn equal_pairs(grid: Vec<Vec<i32>>) -> i32 {
        let n = grid.len();

        // 统计每种行模式出现的次数
        let mut row_count = HashMap::with_capacity(n);
        for row in &grid {
            *row_count.entry(row.as_slice()).or_insert(0) += 1;
        }

        // 遍历每一列，在 map 中查找匹配
        let mut ans = 0;
        for j in 0..n {
            let col: Vec<i32> = (0..n).map(|i| grid[i][j]).collect();
            if let Some(&cnt) = row_count.get(col.as_slice()) {
                ans += cnt;
            }
        }

        ans
    }
}
```
