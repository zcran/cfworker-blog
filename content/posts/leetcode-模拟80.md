---
title: "leetcode-模拟80"
date: 2026-08-08T11:31:13+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 行和列中一和零的差值

给你一个下标从 0 开始的 m x n 二进制矩阵 grid 。

我们按照如下过程，定义一个下标从 0 开始的 m x n 差值矩阵 diff ：

令第 i 行一的数目为 onesRowi 。
令第 j 列一的数目为 onesColj 。
令第 i 行零的数目为 zerosRowi 。
令第 j 列零的数目为 zerosColj 。
diff[i][j] = onesRowi + onesColj - zerosRowi - zerosColj

请你返回差值矩阵 diff 。


```
impl Solution {
   /// 原地计算差值矩阵。
   /// 推导：diff = onesRow + onesCol - zerosRow - zerosCol
   ///             = onesRow + onesCol - (n - onesRow) - (m - onesCol)
   ///             = 2 * (onesRow + onesCol) - (m + n)
   pub fn ones_minus_zeros(mut grid: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
       let m = grid.len();
       let n = grid[0].len();
       let total = (m + n) as i32;

       // 一次遍历同时统计每行、每列的 1 的个数
       let mut row = vec![0; m];
       let mut col = vec![0; n];
       for i in 0..m {
           for j in 0..n {
               let v = grid[i][j];
               row[i] += v;
               col[j] += v;
           }
       }

       // 原地填回差值
       for i in 0..m {
           for j in 0..n {
               grid[i][j] = 2 * (row[i] + col[j]) - total;
           }
       }

       grid
   }
}
```
