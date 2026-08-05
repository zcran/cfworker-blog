---
title: "leetcode-图45"
date: 2026-07-27T10:01:37+08:00
tags: ["leetcode", "图"]
draft: false
---


## 统计每个顶点的度

给你一个大小为 n x n 的二维整数数组 matrix，以邻接矩阵形式表示一个 无向图。该图包含 n 个顶点，编号从 0 到 n - 1。

matrix[i][j] = 1 表示顶点 i 与顶点 j 之间存在一条边。

matrix[i][j] = 0 表示顶点 i 与顶点 j 之间不存在边。

顶点的 度（degree）定义为与该顶点相连的边的数量。

请返回一个长度为 n 的整数数组 ans，其中 ans[i] 表示顶点 i 的度。


```
impl Solution {
    /// 计算无向图邻接矩阵中每个顶点的度
    ///
    /// # 参数
    /// - `matrix`: n x n 的邻接矩阵，matrix[i][j] = 1 表示存在边
    ///
    /// # 返回
    /// 长度为 n 的数组，ans[i] 表示顶点 i 的度
    ///
    /// # 复杂度
    /// - 时间复杂度: O(n²)
    /// - 空间复杂度: O(n) (仅结果数组)
    pub fn find_degrees(matrix: Vec<Vec<i32>>) -> Vec<i32> {
        // 对于无向图，邻接矩阵是对称的，但直接对每行求和即可得到度数
        // 因为 matrix[i][i] = 0（无自环），所以不会重复计算
        matrix
            .into_iter()
            .map(|row| row.into_iter().sum())
            .collect()
    }
}
```
