---
title: "leetcode-前缀和149"
date: 2026-08-25T10:02:55+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 最大子矩阵

给定一个正整数、负整数和 0 组成的 N × M 矩阵，编写代码找出元素总和最大的子矩阵。

返回一个数组 [r1, c1, r2, c2]，其中 r1, c1 分别代表子矩阵左上角的行号和列号，r2, c2 分别代表右下角的行号和列号。若有多个满足条件的子矩阵，返回任意一个均可。


```
impl Solution {
    pub fn get_max_matrix(matrix: Vec<Vec<i32>>) -> Vec<i32> {
        let rows = matrix.len();
        let cols = matrix[0].len();

        // 1. 列前缀和：col_prefix[i][j] = sum of rows 0..i at column j
        let mut col_prefix = vec![vec![0; cols]; rows + 1];
        for i in 0..rows {
            for j in 0..cols {
                col_prefix[i + 1][j] = col_prefix[i][j] + matrix[i][j];
            }
        }

        // 辅助函数：在一维数组中找最大子数组和，返回 (sum, left, right)
        fn max_subarray(arr: &[i32]) -> (i32, usize, usize) {
            let mut max_sum = arr[0];
            let mut cur_sum = arr[0];
            let mut start = 0;
            let mut end = 0;
            let mut temp_start = 0;

            for i in 1..arr.len() {
                if arr[i] > cur_sum + arr[i] {
                    cur_sum = arr[i];
                    temp_start = i;
                } else {
                    cur_sum += arr[i];
                }

                if cur_sum > max_sum {
                    max_sum = cur_sum;
                    start = temp_start;
                    end = i;
                }
            }

            (max_sum, start, end)
        }

        let mut max_sum = matrix[0][0];
        let mut top = 0;
        let mut bottom = 0;
        let mut left = 0;
        let mut right = 0;

        // 2. 枚举上下边界
        for i in 0..rows {
            for j in i + 1..=rows {
                // 构建当前上下边界之间的列和数组
                let mut col_sum = Vec::with_capacity(cols);
                for c in 0..cols {
                    col_sum.push(col_prefix[j][c] - col_prefix[i][c]);
                }

                let (cur_sum, l, r) = max_subarray(&col_sum);

                // 更新最大子矩阵
                if cur_sum > max_sum {
                    max_sum = cur_sum;
                    top = i;
                    bottom = j - 1;
                    left = l;
                    right = r;
                }
            }
        }

        vec![top as i32, left as i32, bottom as i32, right as i32]
    }
}
```
