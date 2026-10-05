---
title: "leetcode-矩阵34"
date: 2026-09-19T10:41:34+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 给定行和列的和求可行矩阵

给你两个非负整数数组 rowSum 和 colSum ，其中 rowSum[i] 是二维矩阵中第 i 行元素的和， colSum[j] 是第 j 列元素的和。换言之你不知道矩阵里的每个元素，但是你知道每一行和每一列的和。

请找到大小为 rowSum.length x colSum.length 的任意 非负整数 矩阵，且该矩阵满足 rowSum 和 colSum 的要求。

请你返回任意一个满足题目要求的二维矩阵，题目保证存在 至少一个 可行矩阵。


```
impl Solution {
    pub fn restore_matrix(mut row_sum: Vec<i32>, mut col_sum: Vec<i32>) -> Vec<Vec<i32>> {
        let rows = row_sum.len();
        let cols = col_sum.len();
        let mut grid = vec![vec![0; cols]; rows];

        // 贪心：每次在 (i, j) 处填入 min(剩余行和, 剩余列和)
        // 该位置填完后，若行或列剩余和为 0，则对应指针前移
        let (mut i, mut j) = (0, 0);
        while i < rows && j < cols {
            let val = row_sum[i].min(col_sum[j]);
            grid[i][j] = val;
            row_sum[i] -= val;
            col_sum[j] -= val;

            if row_sum[i] == 0 {
                i += 1;
            }
            if col_sum[j] == 0 {
                j += 1;
            }
        }

        grid
    }
}
```
