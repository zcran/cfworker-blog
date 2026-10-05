---
title: "leetcode-矩阵28"
date: 2026-09-19T10:41:33+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 矩阵中的幸运数

给你一个 m x n 的矩阵，矩阵中的数字 各不相同 。请你按 任意 顺序返回矩阵中的所有幸运数。

幸运数 是指矩阵中满足同时下列两个条件的元素：

在同一行的所有元素中最小
在同一列的所有元素中最大


```
impl Solution {
    pub fn lucky_numbers(matrix: Vec<Vec<i32>>) -> Vec<i32> {
        let rows = matrix.len();
        let cols = matrix[0].len();

        // 先求出每一列的最大值，避免在内层循环中重复扫描整列
        let mut col_max = vec![i32::MIN; cols];
        for row in &matrix {
            for (j, &v) in row.iter().enumerate() {
                if v > col_max[j] {
                    col_max[j] = v;
                }
            }
        }

        // 遍历每一行，找出该行最小值，若它同时等于所在列的最大值，则为幸运数
        let mut result = Vec::new();
        for row in &matrix {
            let mut min_val = row[0];
            let mut min_idx = 0;
            for (j, &v) in row.iter().enumerate() {
                if v < min_val {
                    min_val = v;
                    min_idx = j;
                }
            }
            if min_val == col_max[min_idx] {
                result.push(min_val);
            }
        }

        result
    }
}
```
