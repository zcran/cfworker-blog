---
title: "leetcode-几何16"
date: 2026-05-11T20:20:55+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 距离顺序排列矩阵单元格

给定四个整数 rows ,   cols ,  rCenter 和 cCenter 。有一个 rows x cols 的矩阵，你在单元格上的坐标是 (rCenter, cCenter) 。

返回矩阵中的所有单元格的坐标，并按与 (rCenter, cCenter) 的 距离 从最小到最大的顺序排。你可以按 任何 满足此条件的顺序返回答案。

单元格(r1, c1) 和 (r2, c2) 之间的距离为|r1 - r2| + |c1 - c2|。

```
use std::iter::FromIterator;

impl Solution {
    pub fn all_cells_dist_order(rows: i32, cols: i32, r_center: i32, c_center: i32) -> Vec<Vec<i32>> {
        let mut cells: Vec<(i32, i32)> = (0..rows)
            .flat_map(|r| (0..cols).map(move |c| (r, c)))
            .collect();
        cells.sort_unstable_by_key(|&(r, c)| (r - r_center).abs() + (c - c_center).abs());
        cells.into_iter().map(|(r, c)| vec![r, c]).collect()
    }
}
```
