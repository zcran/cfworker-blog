---
title: "leetcode-前缀和90"
date: 2026-08-25T10:02:51+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 构造乘积矩阵

给你一个下标从 0 开始、大小为 n * m 的二维整数矩阵 grid ，定义一个下标从 0 开始、大小为 n * m 的的二维矩阵 p。如果满足以下条件，则称 p 为 grid 的 乘积矩阵 ：

对于每个元素 p[i][j] ，它的值等于除了 grid[i][j] 外所有元素的乘积。乘积对 12345 取余数。

返回 grid 的乘积矩阵。


```
impl Solution {
    pub fn construct_product_matrix(grid: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        const MOD: i64 = 12345;
        let (n, m) = (grid.len(), grid[0].len());
        let mut p = vec![vec![0; m]; n];

        // 第一遍：从右下到左上，p[i][j] = 右侧及下方所有元素的乘积
        let mut suffix = 1;
        for i in (0..n).rev() {
            for j in (0..m).rev() {
                p[i][j] = suffix as i32;
                suffix = (suffix * grid[i][j] as i64) % MOD;
            }
        }

        // 第二遍：从左到右，p[i][j] *= 左侧及上方所有元素的乘积
        let mut prefix = 1;
        for i in 0..n {
            for j in 0..m {
                p[i][j] = ((p[i][j] as i64 * prefix) % MOD) as i32;
                prefix = (prefix * grid[i][j] as i64) % MOD;
            }
        }

        p
    }
}
```
