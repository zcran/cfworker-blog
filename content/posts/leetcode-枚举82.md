---
title: "leetcode-枚举82"
date: 2026-07-09T10:05:03+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 等和矩阵分割 I

给你一个由正整数组成的 m x n 矩阵 grid。你的任务是判断是否可以通过 一条水平或一条垂直分割线 将矩阵分割成两部分，使得：

分割后形成的每个部分都是 非空 的。

两个部分中所有元素的和 相等 。

如果存在这样的分割，返回 true；否则，返回 false。


```
impl Solution {
    pub fn can_partition_grid(grid: Vec<Vec<i32>>) -> bool {
        let m = grid.len();
        let n = grid[0].len();

        // 使用一维前缀和优化内存
        let mut row_prefix = vec![vec![0i64; n + 1]; m + 1];
        let mut total = 0i64;

        // 构建二维前缀和
        for i in 0..m {
            for j in 0..n {
                let val = grid[i][j] as i64;
                row_prefix[i + 1][j + 1] = row_prefix[i + 1][j] + row_prefix[i][j + 1]
                                          - row_prefix[i][j] + val;
                total += val;
            }
        }

        // 检查水平分割线：在第 i 行下方分割
        // 上半部分和 = row_prefix[i + 1][n]，下半部分和 = total - 上半部分和
        for i in 0..m - 1 {
            let top_sum = row_prefix[i + 1][n];
            if top_sum * 2 == total {
                return true;
            }
        }

        // 检查垂直分割线：在第 j 列右侧分割
        // 左半部分和 = row_prefix[m][j + 1]，右半部分和 = total - 左半部分和
        for j in 0..n - 1 {
            let left_sum = row_prefix[m][j + 1];
            if left_sum * 2 == total {
                return true;
            }
        }

        false
    }
}
```
