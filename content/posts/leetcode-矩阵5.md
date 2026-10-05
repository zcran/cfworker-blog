---
title: "leetcode-矩阵5"
date: 2026-09-19T10:41:32+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 矩阵置零

给定一个 m x n 的矩阵，如果一个元素为 0 ，则将其所在行和列的所有元素都设为 0 。请使用 原地 算法。


```
impl Solution {
    pub fn set_zeroes(matrix: &mut Vec<Vec<i32>>) {
        let m = matrix.len();
        let n = matrix[0].len();

        // 用矩阵的第一行和第一列作为标记位，实现 O(1) 额外空间。
        // 额外用两个 bool 记录第一行、第一列本身是否需要置零，
        // 因为它们既充当标记位又需要被更新，会相互覆盖。
        let first_row_zero = matrix[0].iter().any(|&x| x == 0);
        let first_col_zero = (0..m).any(|i| matrix[i][0] == 0);

        // 1. 遍历除第一行、第一列外的元素，把标记写到第一行/第一列
        for i in 1..m {
            for j in 1..n {
                if matrix[i][j] == 0 {
                    matrix[i][0] = 0; // 标记第 i 行需要置零
                    matrix[0][j] = 0; // 标记第 j 列需要置零
                }
            }
        }

        // 2. 根据标记置零（从后往前无所谓，这里顺序遍历也可，因为标记位在第一行列）
        for i in 1..m {
            for j in 1..n {
                if matrix[i][0] == 0 || matrix[0][j] == 0 {
                    matrix[i][j] = 0;
                }
            }
        }

        // 3. 单独处理第一行和第一列
        if first_row_zero {
            for j in 0..n {
                matrix[0][j] = 0;
            }
        }
        if first_col_zero {
            for i in 0..m {
                matrix[i][0] = 0;
            }
        }
    }
}
```
