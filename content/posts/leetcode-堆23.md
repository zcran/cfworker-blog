---
title: "leetcode-堆23"
date: 2026-08-21T09:54:12+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 对角线遍历 II

给你一个列表 nums ，里面每一个元素都是一个整数列表。请你依照下面各图的规则，按顺序返回 nums 中对角线上的整数。


```
impl Solution {
    /// 按对角线遍历：同一条对角线上的元素 row+col 相同，从右上到左下输出
    pub fn find_diagonal_order(nums: Vec<Vec<i32>>) -> Vec<i32> {
        // 对角线数量 = max(row + col) + 1
        let max_diag = nums.iter().enumerate()
            .map(|(i, row)| i + row.len())
            .max()
            .unwrap_or(0);

        // 按对角线分组收集
        let mut diagonals: Vec<Vec<i32>> = vec![Vec::new(); max_diag];
        for (i, row) in nums.iter().enumerate() {
            for (j, &val) in row.iter().enumerate() {
                diagonals[i + j].push(val);
            }
        }

        // 每条对角线从后往前输出（因为先遍历到的行号小，后遍历到的行号大）
        diagonals.into_iter()
            .flat_map(|d| d.into_iter().rev())
            .collect()
    }
}
```
