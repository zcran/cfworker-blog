---
title: "leetcode-模拟12"
date: 2026-08-08T11:31:08+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 重塑矩阵

在 MATLAB 中，有一个非常有用的函数 reshape ，它可以将一个 m x n 矩阵重塑为另一个大小不同（r x c）的新矩阵，但保留其原始数据。

给你一个由二维数组 mat 表示的 m x n 矩阵，以及两个正整数 r 和 c ，分别表示想要的重构的矩阵的行数和列数。

重构后的矩阵需要将原始矩阵的所有元素以相同的 行遍历顺序 填充。

如果具有给定参数的 reshape 操作是可行且合理的，则输出新的重塑矩阵；否则，输出原始矩阵。


```
impl Solution {
    pub fn matrix_reshape(mat: Vec<Vec<i32>>, r: i32, c: i32) -> Vec<Vec<i32>> {
        let (r, c) = (r as usize, c as usize);
        let m = mat.len();
        let n = mat[0].len();

        // 元素总数不匹配，无法重塑
        if m * n != r * c {
            return mat;
        }

        // 展平后按新列数切分
        mat.into_iter()
            .flatten()
            .collect::<Vec<_>>()
            .chunks(c)
            .map(|chunk| chunk.to_vec())
            .collect()
    }
}
```
