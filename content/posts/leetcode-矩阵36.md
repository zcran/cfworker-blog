---
title: "leetcode-矩阵36"
date: 2026-09-19T10:41:34+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 重新排列后的最大子矩阵

给你一个二进制矩阵 matrix ，它的大小为 m x n ，你可以将 matrix 中的 列 按任意顺序重新排列。

请你返回最优方案下将 matrix 重新排列后，全是 1 的最大子矩阵面积。


```
impl Solution {
    pub fn largest_submatrix(matrix: Vec<Vec<i32>>) -> i32 {
        let mut matrix = matrix;
        let rows = matrix.len();
        let cols = matrix[0].len();

        // 第一步：把 matrix[i][j] 变为以 (i,j) 结尾、向上连续的 1 的个数
        // 这样每一列转化为一组"柱状图"高度
        for i in 1..rows {
            for j in 0..cols {
                if matrix[i][j] == 1 {
                    matrix[i][j] += matrix[i - 1][j];
                }
            }
        }

        let mut max_area = 0;

        // 第二步：对每一行降序排序，相当于按列高从高到低重排列
        // 排序后 matrix[i][j] 表示：以当前行为底、宽度为 j+1 的子矩阵中最小列高
        for row in matrix.iter_mut() {
            row.sort_unstable_by(|a, b| b.cmp(a));
            for (j, &h) in row.iter().enumerate() {
                max_area = max_area.max(h * (j as i32 + 1));
            }
        }

        max_area
    }
}
```
