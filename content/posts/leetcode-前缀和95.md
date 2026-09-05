---
title: "leetcode-前缀和95"
date: 2026-08-25T10:02:52+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 元素和小于等于 k 的子矩阵的数目

给你一个下标从 0 开始的整数矩阵 grid 和一个整数 k。

返回包含 grid 左上角元素、元素和小于或等于 k 的 子矩阵的数目。


```
impl Solution {
    pub fn count_submatrices(grid: Vec<Vec<i32>>, k: i32) -> i32 {
        let (n, m) = (grid.len(), grid[0].len());
        let mut ans = 0;

        // 直接原地修改 grid 为列前缀和，节省额外内存
        let mut grid = grid;
        for i in 1..n {
            for j in 0..m {
                grid[i][j] += grid[i - 1][j];
            }
        }

        // 枚举每个可能的子矩阵右下角
        for i in 0..n {
            let mut row_sum = 0;
            for j in 0..m {
                row_sum += grid[i][j];
                if row_sum <= k {
                    ans += 1;
                }
            }
        }

        ans
    }
}
```
