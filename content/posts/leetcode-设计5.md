---
title: "leetcode-设计5"
date: 2026-07-14T10:41:24+08:00
tags: ["leetcode", "设计"]
draft: false
---


##  二维区域和检索 - 矩阵不可变

给定一个二维矩阵 matrix，以下类型的多个请求：

计算其子矩形范围内元素的总和，该子矩阵的 左上角 为 (row1, col1) ，右下角 为 (row2, col2) 。

实现 NumMatrix 类：

NumMatrix(int[][] matrix) 给定整数矩阵 matrix 进行初始化

int sumRegion(int row1, int col1, int row2, int col2) 返回 左上角 (row1, col1) 、右下角 (row2, col2) 所描述的子矩阵的元素 总和 。


```
/// 二维前缀和矩阵，支持 O(1) 时间查询子矩阵和
struct NumMatrix {
    /// prefix[i][j] 表示从 matrix[0][0] 到 matrix[i-1][j-1] 的矩形和
    /// 即 prefix[i][j] = sum(matrix[0..i][0..j])
    /// prefix[0][*] 和 prefix[*][0] 均为 0，作为哨兵边界
    prefix: Vec<Vec<i32>>,
}

impl NumMatrix {
    /// 使用二维矩阵初始化前缀和
    ///
    /// 时间复杂度: O(m*n)，空间复杂度: O(m*n)
    pub fn new(matrix: Vec<Vec<i32>>) -> Self {
        let rows = matrix.len();
        let cols = matrix[0].len();

        // 创建 (rows+1) x (cols+1) 的前缀和矩阵，边界填充 0
        let mut prefix = vec![vec![0; cols + 1]; rows + 1];

        for i in 0..rows {
            let mut row_sum = 0;
            for j in 0..cols {
                row_sum += matrix[i][j];
                // 当前前缀和 = 上方前缀和 + 当前行累计和
                prefix[i + 1][j + 1] = prefix[i][j + 1] + row_sum;
            }
        }

        Self { prefix }
    }

    /// 计算子矩阵 (row1, col1) 到 (row2, col2) 的元素和
    ///
    /// 利用容斥原理：
    /// sum = prefix[row2+1][col2+1]
    ///     - prefix[row2+1][col1]
    ///     - prefix[row1][col2+1]
    ///     + prefix[row1][col1]
    ///
    /// 时间复杂度: O(1)，空间复杂度: O(1)
    pub fn sum_region(&self, row1: i32, col1: i32, row2: i32, col2: i32) -> i32 {
        let (r1, c1) = (row1 as usize, col1 as usize);
        let (r2, c2) = (row2 as usize + 1, col2 as usize + 1);

        self.prefix[r2][c2]
            - self.prefix[r2][c1]
            - self.prefix[r1][c2]
            + self.prefix[r1][c1]
    }
}
```
