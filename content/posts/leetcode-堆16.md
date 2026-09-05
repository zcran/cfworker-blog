---
title: "leetcode-堆16"
date: 2026-08-21T09:54:11+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 矩阵中战斗力最弱的 K 行

给你一个大小为 m * n 的矩阵 mat，矩阵由若干军人和平民组成，分别用 1 和 0 表示。

请你返回矩阵中战斗力最弱的 k 行的索引，按从最弱到最强排序。

如果第 i 行的军人数量少于第 j 行，或者两行军人数量相同但 i 小于 j，那么我们认为第 i 行的战斗力比第 j 行弱。

军人 总是 排在一行中的靠前位置，也就是说 1 总是出现在 0 之前。


```
impl Solution {
    /// 利用\"1 总是在 0 之前\"的特性，用 partition_point 二分统计每行 1 的个数
    pub fn k_weakest_rows(mat: Vec<Vec<i32>>, k: i32) -> Vec<i32> {
        let k = k as usize;
        let mut rows: Vec<(usize, usize)> = mat
            .iter()
            .enumerate()
            .map(|(i, row)| (row.partition_point(|&x| x == 1), i))
            .collect();

        // 按 1 的个数升序，个数相同按索引升序
        rows.sort_unstable();

        rows.into_iter()
            .take(k)
            .map(|(_, i)| i as i32)
            .collect()
    }
}
```
