---
title: "leetcode-分治20"
date: 2026-06-13T10:51:38+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 找出第 K 大的异或坐标值


给你一个二维矩阵 matrix 和一个整数 k ，矩阵大小为 m x n 由非负整数组成。

矩阵中坐标 (a, b) 的 目标值 可以通过对所有元素 matrix[i][j] 执行异或运算得到，其中 i 和 j 满足 0 <= i <= a < m 且 0 <= j <= b < n（下标从 0 开始计数）。

请你找出 matrix 的所有坐标中第 k 大的目标值（k 的值从 1 开始计数）。


```
impl Solution {
    /// 找出矩阵中所有子矩阵异或值的第 K 大值
    ///
    /// 子矩阵的异或值定义为：对该子矩阵内所有元素进行异或运算的结果
    ///
    /// # 参数
    /// - `matrix`: 二维矩阵
    /// - `k`: 第 K 大（1-indexed）
    ///
    /// # 返回
    /// - 第 K 大的子矩阵异或值
    ///
    /// # 算法思路
    /// 1. 使用二维前缀异或数组，O(1) 时间计算任意子矩阵的异或值
    /// 2. 收集所有子矩阵的异或值（以每个元素作为右下角）
    /// 3. 使用快速选择（select_nth_unstable）找出第 K 大的值
    pub fn kth_largest_value(matrix: Vec<Vec<i32>>, k: i32) -> i32 {
        let m = matrix.len();          // 矩阵行数
        let n = matrix[0].len();       // 矩阵列数

        // 预分配容量，避免多次扩容
        let mut xor_values = Vec::with_capacity(m * n);

        // 二维前缀异或数组，多一行一列作为哨兵，方便边界处理
        // prefix[i+1][j+1] 表示以 (i,j) 为右下角的子矩阵异或值
        let mut prefix = vec![vec![0; n + 1]; m + 1];

        // 构建前缀异或数组
        for i in 0..m {
            for j in 0..n {
                // 二维前缀异或递推公式：
                // prefix[i+1][j+1] = prefix[i+1][j] ^ prefix[i][j+1] ^ prefix[i][j] ^ matrix[i][j]
                // 原理：包含 (i,j) 的子矩阵异或 = 左边 ^ 上边 ^ 左上角 ^ 当前值
                prefix[i + 1][j + 1] = prefix[i + 1][j]
                                     ^ prefix[i][j + 1]
                                     ^ prefix[i][j]
                                     ^ matrix[i][j];
            }
            // 将当前行的所有子矩阵异或值（以第 i 行元素为右下角）添加到收集数组中
            xor_values.extend_from_slice(&prefix[i + 1][1..n + 1]);
        }

        // 使用快速选择算法找到第 K 大的元素
        // select_nth_unstable 会将数组分区，使得第 n 个位置上的元素是第 n 小的
        // 因此第 K 大对应的索引是 m*n - k
        let kth_index = m * n - k as usize;
        *xor_values.select_nth_unstable(kth_index).1
    }
}
```
