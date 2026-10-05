---
title: "leetcode-矩阵26"
date: 2026-09-19T10:41:33+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 将矩阵按对角线排序

矩阵对角线 是一条从矩阵最上面行或者最左侧列中的某个元素开始的对角线，沿右下方向一直到矩阵末尾的元素。例如，矩阵 mat 有 6 行 3 列，从 mat[2][0] 开始的 矩阵对角线 将会经过 mat[2][0]、mat[3][1] 和 mat[4][2] 。

给你一个 m * n 的整数矩阵 mat ，请你将同一条 矩阵对角线 上的元素按升序排序后，返回排好序的矩阵。


```
impl Solution {
    pub fn diagonal_sort(mut mat: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        let rows = mat.len();
        let cols = mat[0].len();

        // 每条对角线用 i - j + cols 作为唯一索引，取值范围 [0, rows + cols - 1]
        // 收集每条对角线上的所有元素
        let mut diags: Vec<Vec<i32>> = vec![Vec::new(); rows + cols];
        for i in 0..rows {
            for j in 0..cols {
                diags[i + cols - j - 1].push(mat[i][j]);
            }
        }

        // 每条对角线降序排序，便于后续用 pop() 从小到大依次填回
        for d in diags.iter_mut() {
            d.sort_unstable_by(|a, b| b.cmp(a));
        }

        // 按原顺序填回，pop() 弹出的是当前最小值
        for i in 0..rows {
            for j in 0..cols {
                mat[i][j] = diags[i + cols - j - 1].pop().unwrap();
            }
        }

        mat
    }
}
```
