---
title: "leetcode-矩阵62"
date: 2026-09-19T10:41:36+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 修改矩阵

给你一个下标从 0 开始、大小为 m x n 的整数矩阵 matrix ，新建一个下标从 0 开始、名为 answer 的矩阵。使 answer 与 matrix 相等，接着将其中每个值为 -1 的元素替换为所在列的 最大 元素。

返回矩阵 answer 。


```
impl Solution {
    pub fn modified_matrix(matrix: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        let rows = matrix.len();
        let cols = matrix[0].len();

        // 先统计每一列的最大值（-1 不影响，因为题目保证每列至少有一个非 -1 元素）
        let mut col_max = vec![i32::MIN; cols];
        for r in 0..rows {
            for c in 0..cols {
                if matrix[r][c] > col_max[c] {
                    col_max[c] = matrix[r][c];
                }
            }
        }

        // 直接消费 matrix 构造 answer，避免额外 clone 整表
        let mut answer = matrix;
        for r in 0..rows {
            for c in 0..cols {
                if answer[r][c] == -1 {
                    answer[r][c] = col_max[c];
                }
            }
        }

        answer
    }
}
```
