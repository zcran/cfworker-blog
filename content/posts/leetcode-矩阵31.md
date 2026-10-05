---
title: "leetcode-矩阵31"
date: 2026-09-19T10:41:34+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 矩阵对角线元素的和

给你一个正方形矩阵 mat，请你返回矩阵对角线元素的和。

请你返回在矩阵主对角线上的元素和副对角线上且不在主对角线上元素的和。


```
impl Solution {
    pub fn diagonal_sum(mat: Vec<Vec<i32>>) -> i32 {
        let n = mat.len();

        // 累加主对角线 mat[i][i] 与副对角线 mat[i][n-1-i] 上的元素
        // 当 n 为奇数时，中心元素被重复计算一次，需减去
        let mut sum = 0;
        for i in 0..n {
            sum += mat[i][i] + mat[i][n - 1 - i];
        }

        if n % 2 == 1 {
            sum -= mat[n / 2][n / 2];
        }

        sum
    }
}
```
