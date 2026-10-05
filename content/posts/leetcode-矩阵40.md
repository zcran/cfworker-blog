---
title: "leetcode-矩阵40"
date: 2026-09-19T10:41:34+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 最大方阵和

给你一个 n x n 的整数方阵 matrix 。你可以执行以下操作 任意次 ：

选择 matrix 中 相邻 两个元素，并将它们都 乘以 -1 。

如果两个元素有 公共边 ，那么它们就是 相邻 的。

你的目的是 最大化 方阵元素的和。请你在执行以上操作之后，返回方阵的 最大 和。


```
impl Solution {
    pub fn max_matrix_sum(matrix: Vec<Vec<i32>>) -> i64 {
        // 关键观察：通过相邻元素同时取反，可以把任意一个负数"搬运"到任意位置，
        // 因此最终结果只取决于负数个数的奇偶性：
        //   - 负数个数为偶数：全部可变为正数，答案为所有绝对值之和
        //   - 负数个数为奇数：必有一个元素为负，牺牲绝对值最小的那个，答案为总和 - 2 * 最小绝对值
        let mut neg_count = 0;
        let mut total: i64 = 0;
        let mut min_abs = i32::MAX;

        for row in &matrix {
            for &v in row {
                let abs_v = v.abs();
                if v < 0 {
                    neg_count += 1;
                }
                total += abs_v as i64;
                if abs_v < min_abs {
                    min_abs = abs_v;
                }
            }
        }

        if neg_count % 2 == 0 {
            total
        } else {
            total - 2 * min_abs as i64
        }
    }
}
```
