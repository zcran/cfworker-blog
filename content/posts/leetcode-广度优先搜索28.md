---
title: "leetcode-广度优先搜索28"
date: 2026-09-13T09:32:08+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 转化为全零矩阵的最少反转次数

给你一个 m x n 的二进制矩阵 mat。每一步，你可以选择一个单元格并将它反转（反转表示 0 变 1 ，1 变 0 ）。如果存在和它相邻的单元格，那么这些相邻的单元格也会被反转。相邻的两个单元格共享同一条边。

请你返回将矩阵 mat 转化为全零矩阵的最少反转次数，如果无法转化为全零矩阵，请返回 -1 。

二进制矩阵 的每一个格子要么是 0 要么是 1 。

全零矩阵 是所有格子都为 0 的矩阵。


```
impl Solution {
    pub fn min_flips(mat: Vec<Vec<i32>>) -> i32 {
        let (m, n) = (mat.len(), mat[0].len());
        let total = m * n;

        // 把矩阵压成一个整数状态：第 (i, j) 位对应 (i * n + j)
        let mut start = 0usize;
        for i in 0..m {
            for j in 0..n {
                start |= (mat[i][j] as usize) << (i * n + j);
            }
        }

        // 预处理每个格子被翻转时会影响到的位（自己 + 上下左右）
        let mut flip_mask = vec![0usize; total];
        for i in 0..m {
            for j in 0..n {
                let mut mask = 1usize << (i * n + j);
                if i > 0 {
                    mask |= 1usize << ((i - 1) * n + j);
                }
                if i + 1 < m {
                    mask |= 1usize << ((i + 1) * n + j);
                }
                if j > 0 {
                    mask |= 1usize << (i * n + j - 1);
                }
                if j + 1 < n {
                    mask |= 1usize << (i * n + j + 1);
                }
                flip_mask[i * n + j] = mask;
            }
        }

        // 枚举所有可能的翻转方案（共 2^total 种），取最少次数
        let mut ans = i32::MAX;
        for ops in 0..(1usize << total) {
            let mut state = start;
            let mut count = 0;

            for i in 0..total {
                if ops & (1 << i) != 0 {
                    state ^= flip_mask[i];
                    count += 1;
                }
            }

            if state == 0 && count < ans {
                ans = count;
            }
        }

        if ans == i32::MAX { -1 } else { ans }
    }
}
```
