---
title: "leetcode-矩阵2"
date: 2026-09-19T10:41:32+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 旋转图像

给定一个 n × n 的二维矩阵 matrix 表示一个图像。请你将图像顺时针旋转 90 度。

你必须在 原地 旋转图像，这意味着你需要直接修改输入的二维矩阵。请不要 使用另一个矩阵来旋转图像。


```
impl Solution {
    pub fn rotate(matrix: &mut Vec<Vec<i32>>) {
        let n = matrix.len();

        // 原地顺时针旋转 90 度 = 先转置，再水平翻转每一行。
        // 时间复杂度 O(n^2)，空间复杂度 O(1)。

        // 1. 转置矩阵：交换 matrix[i][j] 和 matrix[j][i]
        for i in 0..n {
            for j in (i + 1)..n {
                // 使用 split_at_mut 安全地同时获取两个不同行的可变引用
                let (upper, lower) = matrix.split_at_mut(j);
                std::mem::swap(&mut upper[i][j], &mut lower[0][i]);
            }
        }

        // 2. 水平翻转每一行
        for row in matrix.iter_mut() {
            row.reverse();
        }
    }
}
```
