---
title: "leetcode-前缀和77"
date: 2026-08-25T10:02:50+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 子矩阵元素加 1

给你一个正整数 n ，表示最初有一个 n x n 、下标从 0 开始的整数矩阵 mat ，矩阵中填满了 0 。

另给你一个二维整数数组 query 。针对每个查询 query[i] = [row1i, col1i, row2i, col2i] ，请你执行下述操作：

找出 左上角 为 (row1i, col1i) 且 右下角 为 (row2i, col2i) 的子矩阵，将子矩阵中的 每个元素 加 1 。也就是给所有满足 row1i <= x <= row2i 和 col1i <= y <= col2i 的 mat[x][y] 加 1 。

返回执行完所有操作后得到的矩阵 mat 。


```
impl Solution {
    pub fn range_add_queries(n: i32, queries: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        let n = n as usize;

        // 二维差分数组，大小 (n+1) x (n+1) 用于边界处理
        let mut diff = vec![vec![0; n + 1]; n + 1];

        // 应用每个区间加操作到差分数组
        for q in queries {
            let (r1, c1, r2, c2) = (q[0] as usize, q[1] as usize, q[2] as usize, q[3] as usize);
            diff[r1][c1] += 1;
            diff[r2 + 1][c1] -= 1;
            diff[r1][c2 + 1] -= 1;
            diff[r2 + 1][c2 + 1] += 1;
        }

        // 通过前缀和恢复原矩阵
        let mut mat = vec![vec![0; n]; n];
        for i in 0..n {
            for j in 0..n {
                let top = if i > 0 { mat[i - 1][j] } else { 0 };
                let left = if j > 0 { mat[i][j - 1] } else { 0 };
                let top_left = if i > 0 && j > 0 { mat[i - 1][j - 1] } else { 0 };
                mat[i][j] = diff[i][j] + top + left - top_left;
            }
        }

        mat
    }
}
```
