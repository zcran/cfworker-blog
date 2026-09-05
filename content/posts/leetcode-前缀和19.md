---
title: "leetcode-前缀和19"
date: 2026-08-25T10:02:47+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 矩阵区域和

给你一个 m x n 的矩阵 mat 和一个整数 k ，请你返回一个矩阵 answer ，其中每个 answer[i][j] 是所有满足下述条件的元素 mat[r][c] 的和：

i - k <= r <= i + k,
j - k <= c <= j + k 且
(r, c) 在矩阵内。


```
impl Solution {
    pub fn matrix_block_sum(mat: Vec<Vec<i32>>, k: i32) -> Vec<Vec<i32>> {
        let m = mat.len();
        let n = mat[0].len();

        // 二维前缀和：prefix[i+1][j+1] = sum of mat[0..i][0..j]
        let mut prefix = vec![vec![0; n + 1]; m + 1];
        for i in 0..m {
            let mut row_sum = 0;
            for j in 0..n {
                row_sum += mat[i][j];
                prefix[i + 1][j + 1] = prefix[i][j + 1] + row_sum;
            }
        }

        // 查询矩形和 [r1..=r2, c1..=c2]
        let rect_sum = |r1: usize, c1: usize, r2: usize, c2: usize| -> i32 {
            prefix[r2 + 1][c2 + 1] - prefix[r1][c2 + 1] - prefix[r2 + 1][c1] + prefix[r1][c1]
        };

        let mut result = vec![vec![0; n]; m];
        for i in 0..m {
            for j in 0..n {
                // 计算块边界，限制在矩阵范围内
                let r1 = i.saturating_sub(k as usize);
                let c1 = j.saturating_sub(k as usize);
                let r2 = (i + k as usize).min(m - 1);
                let c2 = (j + k as usize).min(n - 1);
                result[i][j] = rect_sum(r1, c1, r2, c2);
            }
        }

        result
    }
}
```
