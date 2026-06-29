---
title: "leetcode-有序集合2"
date: 2026-06-14T10:47:41+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 矩形区域不超过 K 的最大数值和

给你一个 m x n 的矩阵 matrix 和一个整数 k ，找出并返回矩阵内部矩形区域的不超过 k 的最大数值和。

题目数据保证总会存在一个数值和不超过 k 的矩形区域。


```
use std::collections::BTreeSet;

impl Solution {
    /// 找到矩阵中矩形区域和不超过 k 的最大值
    ///
    /// # 算法思路
    /// 1. 固定上下边界，将二维问题转化为一维数组的最大子数组和问题
    /// 2. 使用前缀和快速计算列方向的和
    /// 3. 对每对上下边界，使用 BTreeSet 优化查找 <= k 的最大和
    ///
    /// # 复杂度
    /// - 时间复杂度: O(m²·n·log n)，m 为行数，n 为列数
    /// - 空间复杂度: O(n)
    ///
    /// # 示例
    /// ```
    /// let matrix = vec![vec![1, 0, 1], vec![0, -2, 3]];
    /// assert_eq!(Solution::max_sum_submatrix(matrix, 2), 2);
    /// ```
    pub fn max_sum_submatrix(matrix: Vec<Vec<i32>>, k: i32) -> i32 {
        let rows = matrix.len();
        let cols = matrix[0].len();
        let mut ans = i32::MIN;

        // 枚举上边界
        for top in 0..rows {
            // col_sum[j] 表示从 top 到当前 bottom 行，第 j 列的累加和
            let mut col_sum = vec![0; cols];

            // 枚举下边界
            for bottom in top..rows {
                // 更新列累加和
                for j in 0..cols {
                    col_sum[j] += matrix[bottom][j];
                }

                // 现在问题转化为：在 col_sum 数组中找到不超过 k 的最大子数组和
                // 使用 BTreeSet 存储前缀和，查找前缀和 >= current_sum - k
                let mut prefix_sum = 0;
                let mut prefix_sums = BTreeSet::new();
                prefix_sums.insert(0);  // 空子数组的前缀和为0

                for &val in &col_sum {
                    prefix_sum += val;

                    // 查找最小的前缀和 p，使得 current_sum - p <= k
                    // 即 p >= current_sum - k
                    let target = prefix_sum - k;
                    if let Some(&p) = prefix_sums.range(target..).next() {
                        ans = ans.max(prefix_sum - p);
                        if ans == k {
                            return k;  // 找到最大值，提前返回
                        }
                    }

                    prefix_sums.insert(prefix_sum);
                }
            }
        }

        ans
    }
}
```
