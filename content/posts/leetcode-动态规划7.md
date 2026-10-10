---
title: "leetcode-动态规划7"
date: 2026-10-05T20:39:54+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 杨辉三角 II

给定一个非负索引 rowIndex，返回「杨辉三角」的第 rowIndex 行。

在「杨辉三角」中，每个数是它左上方和右上方的数的和。


```
impl Solution {
    pub fn get_row(row_index: i32) -> Vec<i32> {
        let n = row_index as usize;
        let mut row = vec![1i32; n + 1];

        // 从后往前更新，避免覆盖上一行还未使用的值
        for i in 1..=n {
            for j in (1..i).rev() {
                row[j] += row[j - 1];
            }
        }

        row
    }
}
```
