---
title: "leetcode-矩阵45"
date: 2026-09-19T10:41:35+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 检查是否每一行每一列都包含全部整数

对一个大小为 n x n 的矩阵而言，如果其每一行和每一列都包含从 1 到 n 的 全部 整数（含 1 和 n），则认为该矩阵是一个 有效 矩阵。

给你一个大小为 n x n 的整数矩阵 matrix ，请你判断矩阵是否为一个有效矩阵：如果是，返回 true ；否则，返回 false 。


```
impl Solution {
    /// 判断 n×n 矩阵是否为有效矩阵：每行、每列都恰好包含 1..=n。
    ///
    /// 优化点：
    ///   - 用长度为 n+1 的 bool 数组代替 HashSet，避免哈希开销（O(n²) → O(n²) 但常数更小、更省内存）。
    ///   - 行、列校验复用同一个标记数组，每轮重置。
    ///   - 只校验 1..=n，超出范围的数立即判非法（可选，加快提前返回）。
    pub fn check_valid(matrix: Vec<Vec<i32>>) -> bool {
        let n = matrix.len();
        let mut seen = vec![false; n + 1];

        // 逐行校验
        for row in &matrix {
            seen.iter_mut().for_each(|v| *v = false);
            for &v in row {
                if v < 1 || v as usize > n || seen[v as usize] {
                    return false;
                }
                seen[v as usize] = true;
            }
        }

        // 逐列校验
        for j in 0..n {
            seen.iter_mut().for_each(|v| *v = false);
            for i in 0..n {
                let v = matrix[i][j];
                if v < 1 || v as usize > n || seen[v as usize] {
                    return false;
                }
                seen[v as usize] = true;
            }
        }

        true
    }
}
```
