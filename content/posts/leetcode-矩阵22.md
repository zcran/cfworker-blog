---
title: "leetcode-矩阵22"
date: 2026-09-19T10:41:33+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 重构 2 行二进制矩阵

给你一个 2 行 n 列的二进制数组：

矩阵是一个二进制矩阵，这意味着矩阵中的每个元素不是 0 就是 1。
第 0 行的元素之和为 upper。
第 1 行的元素之和为 lower。
第 i 列（从 0 开始编号）的元素之和为 colsum[i]，colsum 是一个长度为 n 的整数数组。

你需要利用 upper，lower 和 colsum 来重构这个矩阵，并以二维整数数组的形式返回它。

如果有多个不同的答案，那么任意一个都可以通过本题。

如果不存在符合要求的答案，就请返回一个空的二维数组。


```
impl Solution {
    pub fn reconstruct_matrix(mut upper: i32, mut lower: i32, colsum: Vec<i32>) -> Vec<Vec<i32>> {
        let n = colsum.len();
        let mut ret = vec![vec![0; n]; 2];

        for i in 0..n {
            match colsum[i] {
                // 该列两行都必须为 1
                2 => {
                    ret[0][i] = 1;
                    ret[1][i] = 1;
                    upper -= 1;
                    lower -= 1;
                }
                // 该列恰好一个 1，优先分配给当前剩余和较大的一行
                1 => {
                    if upper >= lower {
                        ret[0][i] = 1;
                        upper -= 1;
                    } else {
                        ret[1][i] = 1;
                        lower -= 1;
                    }
                }
                // colsum[i] == 0，两行都为 0，无需处理
                _ => {}
            }

            // 提前剪枝：若某行剩余和小于 0，则无解
            if upper < 0 || lower < 0 {
                return vec![];
            }
        }

        // 最终两行的和都必须恰好用完
        if upper == 0 && lower == 0 {
            ret
        } else {
            vec![]
        }
    }
}
```
