---
title: "leetcode-矩阵54"
date: 2026-09-19T10:41:35+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 找出叠涂元素

给你一个下标从 0 开始的整数数组 arr 和一个 m x n 的整数 矩阵 mat 。arr 和 mat 都包含范围 [1，m * n] 内的 所有 整数。

从下标 0 开始遍历 arr 中的每个下标 i ，并将包含整数 arr[i] 的 mat 单元格涂色。

请你找出 arr 中第一个使得 mat 的某一行或某一列都被涂色的元素，并返回其下标 i 。


```
impl Solution {
    /// 把 mat 中每个值映射到其位置，然后按 arr 顺序涂色，
    /// 用行/列计数器判断是否某行或某列被涂满，返回首个满足的下标。
    ///
    /// 关键点：
    ///   - 值范围是 1..=m*n，可用「值 - 1」直接作为数组下标，避免 HashMap，快且省内存。
    ///   - 用 `pos[val] = i * n + j` 压成一维，比存 [i, j] 更省。
    ///   - 计数达到 n（整行）或 m（整列）即命中。
    pub fn first_complete_index(arr: Vec<i32>, mat: Vec<Vec<i32>>) -> i32 {
        let m = mat.len();
        let n = mat[0].len();

        // pos[v-1] = 该值在 mat 中的线性下标 i*n+j
        let mut pos = vec![0usize; m * n];
        for (i, row) in mat.iter().enumerate() {
            for (j, &v) in row.iter().enumerate() {
                pos[v as usize - 1] = i * n + j;
            }
        }

        let mut row_cnt = vec![0i32; m];
        let mut col_cnt = vec![0i32; n];

        for (k, &v) in arr.iter().enumerate() {
            let p = pos[v as usize - 1];
            let i = p / n;
            let j = p % n;

            row_cnt[i] += 1;
            col_cnt[j] += 1;
            // 整行涂满：该行计数达到 n；整列涂满：该列计数达到 m
            if row_cnt[i] == n as i32 || col_cnt[j] == m as i32 {
                return k as i32;
            }
        }

        -1
    }
}
```
